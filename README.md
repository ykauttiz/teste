import discord
from discord.ext import commands
from discord import app_commands
import json
import asyncio
from typing import Optional

import database as db
from config import (
    MATCH_MODES, WIN_TROPHIES, LOSS_TROPHIES,
    PARTIDAS_CHANNEL_ID, LOG_CHANNEL_ID, RESULTADOS_CHANNEL_ID,
    LOBBY_TIMEOUT_MINUTES, ADMIN_ROLE_IDS, HOSTER_ROLE_IDS
)
from utils.embeds import (
    lobby_embed, result_embed, error_embed, success_embed, info_embed
)


def players_per_side(mode: str) -> int:
    return int(mode[0])  # "3v3" → 3


def has_staff(member: discord.Member) -> bool:
    role_ids = set(ADMIN_ROLE_IDS + HOSTER_ROLE_IDS)
    return any(r.id in role_ids for r in member.roles) or member.guild_permissions.administrator


def has_hoster_role(member: discord.Member) -> bool:
    """Verifica se o membro tem cargo de hoster."""
    return any(r.id in HOSTER_ROLE_IDS for r in member.roles)


def has_admin(member: discord.Member) -> bool:
    """Verifica se é admin (apenas ADMIN_ROLE_IDS, não inclui hosters)."""
    return any(r.id in ADMIN_ROLE_IDS for r in member.roles) or member.guild_permissions.administrator


# ──────────────────────────────────────────────────────────────────
#  VIEWS
# ──────────────────────────────────────────────────────────────────

class LobbyView(discord.ui.View):
    def __init__(self, match_id: str, mode: str, host: discord.Member, cog):
        super().__init__(timeout=LOBBY_TIMEOUT_MINUTES * 60)
        self.match_id  = match_id
        self.mode      = mode
        self.host      = host
        self.cog       = cog

    # ── Entrar no bando Vermelho ──────────────────────────────────
    @discord.ui.button(label="🔴 Entrar — Bando Vermelho", style=discord.ButtonStyle.danger, row=0)
    async def join_red(self, interaction: discord.Interaction, button: discord.ui.Button):
        await self.cog.handle_join(interaction, self.match_id, "red")

    # ── Entrar no bando Azul ─────────────────────────────────────
    @discord.ui.button(label="🔵 Entrar — Bando Azul", style=discord.ButtonStyle.primary, row=0)
    async def join_blue(self, interaction: discord.Interaction, button: discord.ui.Button):
        await self.cog.handle_join(interaction, self.match_id, "blue")

    # ── Entrar com Parceiro ──────────────────────────────────────────
    @discord.ui.button(label="🤠 Entrar com Parceiro", style=discord.ButtonStyle.secondary, row=1)
    async def join_duo(self, interaction: discord.Interaction, button: discord.ui.Button):
        await self.cog.handle_join_duo(interaction, self.match_id)

    # ── Cancelar duelo ────────────────────────────────────────
    @discord.ui.button(label="❌ Cancelar Duelo", style=discord.ButtonStyle.secondary, row=1)
    async def cancel_match(self, interaction: discord.Interaction, button: discord.ui.Button):
        if interaction.user.id != self.host.id and not has_staff(interaction.user):
            await interaction.response.send_message(
                embed=error_embed("🚫 Apenas o Xerife (host) ou Staff pode cancelar o duelo."), ephemeral=True
            )
            return
        await self.cog.do_cancel(interaction, self.match_id)

    async def on_timeout(self):
        match = await db.get_match(self.match_id)
        if match and match["status"] == "waiting":
            await db.update_match(self.match_id, status="cancelled")
            try:
                channel = self.cog.bot.get_channel(match["channel_id"])
                msg     = await channel.fetch_message(match["message_id"])
                await msg.edit(
                    embed=info_embed("⏰ Duelo Expirado",
                                     f"🔫 O duelo `{self.match_id}` foi cancelado por inatividade.\n"
                                     f"Os pistoleiros demoraram demais para se reunir no saloon."),
                    view=None
                )
            except Exception:
                pass


class ResultView(discord.ui.View):
    """View para aguardar o Sheriff (Admin) definir o resultado do duelo."""
    def __init__(self, match_id: str, team_red: list, team_blue: list, cog):
        super().__init__(timeout=None)  # Sem timeout - aguarda admin
        self.match_id  = match_id
        self.team_red  = team_red
        self.team_blue = team_blue
        self.cog       = cog

    @discord.ui.button(label="🏆 Aguardando Sheriff (Admin)", style=discord.ButtonStyle.success, row=0, disabled=True)
    async def waiting_result(self, interaction: discord.Interaction, button: discord.ui.Button):
        pass  # Botão desabilitado - apenas informativo


class SubView(discord.ui.View):
    def __init__(self, match_id: str, original_player_id: int, team: str, cog):
        super().__init__(timeout=120)
        self.match_id           = match_id
        self.original_player_id = original_player_id
        self.team               = team
        self.cog                = cog

    @discord.ui.button(label="✅ Aceitar Substituição", style=discord.ButtonStyle.success)
    async def accept_sub(self, interaction: discord.Interaction, button: discord.ui.Button):
        await self.cog.do_sub(
            interaction, self.match_id,
            self.original_player_id, interaction.user, self.team
        )
        self.stop()


# ──────────────────────────────────────────────────────────────────
#  COG
# ──────────────────────────────────────────────────────────────────

class Matches(commands.Cog):
    def __init__(self, bot: commands.Bot):
        self.bot = bot

    # ──────────────── /host ────────────────────────────────────
    @app_commands.command(name="host", description="🤠 Cria um novo duelo ranqueado no saloon.")
    @app_commands.describe(modo="Modo do duelo: 1v1, 2v2, 3v3, 4v4 ou 5v5")
    @app_commands.choices(modo=[
        app_commands.Choice(name=m, value=m) for m in MATCH_MODES
    ])
    async def host(self, interaction: discord.Interaction, modo: str):
        # Verificar se tem cargo de hoster
        if not has_hoster_role(interaction.user):
            await interaction.response.send_message(
                embed=error_embed("🚫 Apenas Xerifes (Hosters) podem criar duelos!\n"
                                "Quer ser um Hoster? Abra um ticket no canal de suporte."), 
                ephemeral=True
            )
            return

        await db.upsert_player(interaction.user.id, interaction.user.display_name)

        if await db.is_blacklisted(interaction.user.id):
            await interaction.response.send_message(
                embed=error_embed("🚫 Você está na lista de procurados e não pode criar duelos."), ephemeral=True
            )
            return

        # Verificar duelo ativo do usuário
        active = await db.get_active_matches()
        for m in active:
            all_players = json.loads(m["team_red"]) + json.loads(m["team_blue"])
            if interaction.user.id in all_players or m["host_id"] == interaction.user.id:
                await interaction.response.send_message(
                    embed=error_embed("🤠 Você já está em um duelo ativo! Termine primeiro."), ephemeral=True
                )
                return

        channel = interaction.channel
        if PARTIDAS_CHANNEL_ID and channel.id != PARTIDAS_CHANNEL_ID:
            channel = interaction.guild.get_channel(PARTIDAS_CHANNEL_ID) or channel

        match_id = await db.create_match(modo, interaction.user.id, channel.id)

        view  = LobbyView(match_id, modo, interaction.user, self)
        embed = lobby_embed(match_id, modo, interaction.user, [], [])

        await interaction.response.send_message("✅ Duelo criado no saloon!", ephemeral=True)
        msg = await channel.send(embed=embed, view=view)
        
        # Envia notificação @everyone para novo duelo
        notif_embed = discord.Embed(
            title=f"🔫 NOVO DUELO NO SALOON!",
            description=f"@everyone\n```yml\nUm novo duelo foi criado! Entrem no saloon!\n```",
            color=0xFFD700,
            timestamp=datetime.utcnow()
        )
        notif_embed.add_field(
            name="🎯 Modo",
            value=f"```apache\n{modo.upper()}\n```",
            inline=True
        )
        notif_embed.add_field(
            name="🤠 Xerife",
            value=f"```apache\n{interaction.user.display_name}\n```",
            inline=True
        )
        notif_embed.add_field(
            name="📋 ID do Duelo",
            value=f"```apache\n{match_id}\n```",
            inline=True
        )
        notif_embed.add_field(
            name="💡 Como entrar",
            value=f"```yaml\nClique no botão abaixo da mensagem do duelo para entrar no Bando Vermelho ou Azul!\n```",
            inline=False
        )
        notif_embed.set_footer(text=f"🤠 AR2 Sheriff Bot • Duelo criado")
        
        # Envia a notificação no canal
        await channel.send(content="@everyone", embed=notif_embed, delete_after=300)  # Deleta após 5 minutos

        await db.update_match(match_id, message_id=msg.id, channel_id=channel.id)
        await self._log(interaction.guild, f"🆕 Novo duelo `{match_id}` criado por {interaction.user.mention} — modo `{modo}`")

    # ──────────── handle_join ───────────────────────────────────
    async def handle_join(self, interaction: discord.Interaction, match_id: str, team: str):
        await db.upsert_player(interaction.user.id, interaction.user.display_name)

        if await db.is_blacklisted(interaction.user.id):
            await interaction.response.send_message(
                embed=error_embed("🚫 Você está na lista de procurados (blacklist)."), ephemeral=True
            )
            return

        match = await db.get_match(match_id)
        if not match or match["status"] != "waiting":
            await interaction.response.send_message(
                embed=error_embed("🌵 Este duelo não está mais disponível no saloon."), ephemeral=True
            )
            return

        per_side = players_per_side(match["mode"])
        team_red  = json.loads(match["team_red"])
        team_blue = json.loads(match["team_blue"])
        all_players = team_red + team_blue

        if interaction.user.id in all_players:
            await interaction.response.send_message(
                embed=error_embed("🤠 Você já está neste duelo, pistoleiro!"), ephemeral=True
            )
            return

        if team == "red" and len(team_red) >= per_side:
            await interaction.response.send_message(
                embed=error_embed("🌵 O Bando Vermelho já está lotado!"), ephemeral=True
            )
            return
        if team == "blue" and len(team_blue) >= per_side:
            await interaction.response.send_message(
                embed=error_embed("🌵 O Bando Azul já está lotado!"), ephemeral=True
            )
            return

        if team == "red":
            team_red.append(interaction.user.id)
        else:
            team_blue.append(interaction.user.id)

        await db.update_match(match_id, team_red=json.dumps(team_red), team_blue=json.dumps(team_blue))

        # Atualizar embed
        host = interaction.guild.get_member(match["host_id"])
        red_mentions  = [f"<@{uid}>" for uid in team_red]
        blue_mentions = [f"<@{uid}>" for uid in team_blue]

        status = "waiting"
        if len(team_red) == per_side and len(team_blue) == per_side:
            status = "in_progress"
            await db.update_match(match_id, status="in_progress")

        embed = lobby_embed(match_id, match["mode"], host, red_mentions, blue_mentions, status)

        try:
            msg = await interaction.channel.fetch_message(match["message_id"])
            if status == "in_progress":
                result_view = ResultView(match_id, team_red, team_blue, self)
                await msg.edit(embed=embed, view=result_view)
                await interaction.response.send_message(
                    embed=success_embed(f"🎯 Saloon loto! Duelo `{match_id}` em andamento.\n"
                                       f"🏆 Aguardando o Sheriff definir o vencedor..."),
                    ephemeral=True
                )
                await self._log(interaction.guild, f"🔫 Duelo `{match_id}` iniciado no saloon!")
            else:
                await msg.edit(embed=embed)
                await interaction.response.send_message(
                    embed=success_embed(f"🤠 Você entrou no {'Bando Vermelho 🔴' if team=='red' else 'Bando Azul 🔵'}!"),
                    ephemeral=True
                )
        except Exception as e:
            await interaction.response.send_message(embed=error_embed(f"🌵 Erro ao atualizar duelo: {e}"), ephemeral=True)

    # ──────────── handle_join_duo ───────────────────────────────
    async def handle_join_duo(self, interaction: discord.Interaction, match_id: str):
        duo_id = await db.get_duo(interaction.user.id)
        if not duo_id:
            await interaction.response.send_message(
                embed=error_embed("🤠 Você não tem um parceiro de duelo. Use `/duo` para convidar alguém."), ephemeral=True
            )
            return

        match = await db.get_match(match_id)
        if not match:
            await interaction.response.send_message(embed=error_embed("🌵 Duelo não encontrado no saloon."), ephemeral=True)
            return

        per_side   = players_per_side(match["mode"])
        team_red   = json.loads(match["team_red"])
        team_blue  = json.loads(match["team_blue"])
        all_players = team_red + team_blue

        if interaction.user.id in all_players or duo_id in all_players:
            await interaction.response.send_message(embed=error_embed("🤠 Um dos parceiros já está neste duelo."), ephemeral=True)
            return

        # Decidir bando com mais espaço
        red_space  = per_side - len(team_red)
        blue_space = per_side - len(team_blue)

        if red_space >= 2:
            team_red += [interaction.user.id, duo_id]
            await db.update_match(match_id, team_red=json.dumps(team_red))
            team_name = "🔴 Bando Vermelho"
        elif blue_space >= 2:
            team_blue += [interaction.user.id, duo_id]
            await db.update_match(match_id, team_blue=json.dumps(team_blue))
            team_name = "🔵 Bando Azul"
        else:
            await interaction.response.send_message(embed=error_embed("🌵 Não há espaço para dois pistoleiros em nenhum bando."), ephemeral=True)
            return

        host = interaction.guild.get_member(match["host_id"])
        red_m  = [f"<@{u}>" for u in json.loads(match["team_red"])]
        blue_m = [f"<@{u}>" for u in json.loads(match["team_blue"])]

        status = "waiting"
        if len(json.loads(match["team_red"])) == per_side and len(json.loads(match["team_blue"])) == per_side:
            status = "in_progress"
            await db.update_match(match_id, status="in_progress")

        embed = lobby_embed(match_id, match["mode"], host, red_m, blue_m, status)
        msg = await interaction.channel.fetch_message(match["message_id"])

        if status == "in_progress":
            t_red  = json.loads((await db.get_match(match_id))["team_red"])
            t_blue = json.loads((await db.get_match(match_id))["team_blue"])
            await msg.edit(embed=embed, view=ResultView(match_id, t_red, t_blue, self))
        else:
            await msg.edit(embed=embed)

        await interaction.response.send_message(
            embed=success_embed(f"🤠 Você e seu parceiro entraram no {team_name}!"), ephemeral=True
        )

    # ──────────── handle_vote ───────────────────────────────────
    # NOTA: Sistema de votação removido - apenas admins podem definir resultado via /resultado
    # Mantido para compatibilidade caso necessário no futuro
    async def handle_vote(self, interaction: discord.Interaction, match_id: str, vote: str,
                          team_red: list, team_blue: list):
        await interaction.response.send_message(
            embed=error_embed("🏆 Apenas o Sheriff (Admin) pode definir o resultado do duelo.\n"
                            "Use o comando `/resultado` se você for um Admin."), ephemeral=True
        )

    # ──────────── finish_match ──────────────────────────────────
    async def finish_match(self, interaction, match_id: str, winner: str,
                           team_red: list, team_blue: list, mode: str):
        win_t  = int(await db.get_config("win_trophies",  str(WIN_TROPHIES)))
        loss_t = int(await db.get_config("loss_trophies", str(LOSS_TROPHIES)))

        winners = team_red  if winner == "red"  else team_blue
        losers  = team_blue if winner == "red"  else team_red

        for uid in winners:
            await db.record_win(uid, win_t)
            p = await db.get_player(uid)
            await db.add_history(match_id, uid, "win", p["trophies"], win_t)

        for uid in losers:
            await db.record_loss(uid, loss_t)
            p = await db.get_player(uid)
            await db.add_history(match_id, uid, "loss", p["trophies"], -loss_t)

        await db.update_match(match_id, status="finished", winner=winner,
                              finished_at="datetime('now')")

        red_mentions  = [f"<@{u}>" for u in team_red]
        blue_mentions = [f"<@{u}>" for u in team_blue]
        embed = result_embed(match_id, mode, winner, red_mentions, blue_mentions, win_t, loss_t)

        # Atualizar mensagem do lobby
        match = await db.get_match(match_id)
        try:
            ch  = interaction.guild.get_channel(match["channel_id"])
            msg = await ch.fetch_message(match["message_id"])
            await msg.edit(embed=embed, view=None)
        except Exception:
            pass

        # Postar no canal de resultados se configurado
        res_ch_id = int(await db.get_config("resultados_channel", str(RESULTADOS_CHANNEL_ID or 0)))
        if res_ch_id:
            res_ch = interaction.guild.get_channel(res_ch_id)
            if res_ch:
                await res_ch.send(embed=embed)

        await self._log(interaction.guild,
                        f"🏆 Duelo `{match_id}` encerrado — {'🔴 Bando Vermelho' if winner=='red' else '🔵 Bando Azul'} venceu o tiroteio!")

    # ──────────── do_cancel ─────────────────────────────────────
    async def do_cancel(self, interaction: discord.Interaction, match_id: str):
        await db.update_match(match_id, status="cancelled")
        match = await db.get_match(match_id)
        try:
            ch  = interaction.guild.get_channel(match["channel_id"])
            msg = await ch.fetch_message(match["message_id"])
            await msg.edit(
                embed=info_embed("❌ Duelo Cancelado", f"🔫 O duelo `{match_id}` foi cancelado no saloon.\n"
                                f"Os pistoleiros foram para casa."),
                view=None
            )
        except Exception:
            pass
        await interaction.response.send_message(
            embed=success_embed(f"🤠 Duelo `{match_id}` cancelado com sucesso."), ephemeral=True
        )
        await self._log(interaction.guild, f"❌ Duelo `{match_id}` cancelado por {interaction.user.mention}")

    # ──────────── /cancelar ─────────────────────────────────────
    @app_commands.command(name="cancelar", description="[🤠 Sheriff] Cancela um duelo pelo ID.")
    @app_commands.describe(match_id="ID do duelo")
    async def cancelar(self, interaction: discord.Interaction, match_id: str):
        if not has_staff(interaction.user):
            await interaction.response.send_message(embed=error_embed("🚫 Apenas Staff pode cancelar duelos."), ephemeral=True)
            return
        match = await db.get_match(match_id.upper())
        if not match:
            await interaction.response.send_message(embed=error_embed("🌵 Duelo não encontrado no saloon."), ephemeral=True)
            return
        await self.do_cancel(interaction, match_id.upper())

    # ──────────── /resultado ────────────────────────────────────
    @app_commands.command(name="resultado", description="[🤠 Sheriff] Define o resultado final de um duelo.")
    @app_commands.describe(match_id="ID do duelo", bando="Bando vencedor")
    @app_commands.choices(bando=[
        app_commands.Choice(name="🔴 Bando Vermelho", value="red"),
        app_commands.Choice(name="🔵 Bando Azul",    value="blue"),
    ])
    async def resultado(self, interaction: discord.Interaction, match_id: str, bando: str):
        # APENAS ADMINS podem definir resultado - não hosters
        if not has_admin(interaction.user):
            await interaction.response.send_message(
                embed=error_embed("🚫 Apenas Sheriffs (Admins) podem definir o resultado dos duelos!\n"
                                "Hosters podem criar duelos, mas apenas Admins podem dar o resultado final."), 
                ephemeral=True
            )
            return
        match = await db.get_match(match_id.upper())
        if not match or match["status"] not in ("waiting", "in_progress"):
            await interaction.response.send_message(
                embed=error_embed("🌵 Duelo inválido ou já finalizado."), ephemeral=True
            )
            return
        t_red  = json.loads(match["team_red"])
        t_blue = json.loads(match["team_blue"])
        await db.update_match(match_id.upper(), status="in_progress")
        await self.finish_match(interaction, match_id.upper(), bando, t_red, t_blue, match["mode"])
        await interaction.response.send_message(
            embed=success_embed(f"🏆 Resultado registrado! O {'Bando Vermelho' if bando=='red' else 'Bando Azul'} venceu o duelo!"), 
            ephemeral=True
        )

    # ──────────── /substituir ───────────────────────────────────
    @app_commands.command(name="substituir", description="🤠 Solicita substituição em um duelo.")
    @app_commands.describe(match_id="ID do duelo", substituto="Pistoleiro substituto")
    async def substituir(self, interaction: discord.Interaction, match_id: str, substituto: discord.Member):
        match = await db.get_match(match_id.upper())
        if not match or match["status"] != "in_progress":
            await interaction.response.send_message(embed=error_embed("🌵 Duelo inválido ou não está em andamento."), ephemeral=True)
            return

        t_red  = json.loads(match["team_red"])
        t_blue = json.loads(match["team_blue"])

        if interaction.user.id in t_red:
            team = "red"
        elif interaction.user.id in t_blue:
            team = "blue"
        else:
            await interaction.response.send_message(embed=error_embed("🤠 Você não está neste duelo."), ephemeral=True)
            return

        view = SubView(match_id.upper(), interaction.user.id, team, self)
        await interaction.response.send_message(
            content=substituto.mention,
            embed=info_embed("🤠 Pedido de Substituição",
                             f"{interaction.user.mention} quer ser substituído por você no duelo `{match_id}`.\n"
                             f"Clique em **Aceitar** para entrar no tiroteio no lugar dele."),
            view=view
        )

    async def do_sub(self, interaction: discord.Interaction, match_id: str,
                     original_id: int, new_player: discord.Member, team: str):
        if await db.is_blacklisted(new_player.id):
            await interaction.response.send_message(embed=error_embed("🚫 Este pistoleiro está na lista de procurados."), ephemeral=True)
            return

        match = await db.get_match(match_id)
        t_red  = json.loads(match["team_red"])
        t_blue = json.loads(match["team_blue"])

        if team == "red" and original_id in t_red:
            t_red[t_red.index(original_id)] = new_player.id
        elif team == "blue" and original_id in t_blue:
            t_blue[t_blue.index(original_id)] = new_player.id
        else:
            await interaction.response.send_message(embed=error_embed("🌵 Substituição inválida."), ephemeral=True)
            return

        await db.upsert_player(new_player.id, new_player.display_name)
        await db.update_match(match_id, team_red=json.dumps(t_red), team_blue=json.dumps(t_blue))
        await interaction.response.send_message(
            embed=success_embed(f"🤠 {new_player.mention} substituiu <@{original_id}> no duelo `{match_id}`!"),
        )

    # ──────────── helper ────────────────────────────────────────
    async def _log(self, guild: discord.Guild, msg: str):
        log_ch_id = int(await db.get_config("log_channel", str(LOG_CHANNEL_ID or 0)))
        if log_ch_id:
            ch = guild.get_channel(log_ch_id)
            if ch:
                await ch.send(embed=discord.Embed(description=msg, color=0x5865F2))


async def setup(bot: commands.Bot):
    await bot.add_cog(Matches(bot))
