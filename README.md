# 🏆 AR2 Ranked Bot — Guia Completo

Bot de partidas ranqueadas para servidores Discord do jogo **Apocalypse Rising 2 (Roblox)**.
Sistema completo de matchmaking, troféus, divisões, duo e blacklist.

---

## 📋 Índice
1. [Requisitos](#requisitos)
2. [Instalação](#instalação)
3. [Configuração Inicial](#configuração-inicial)
4. [Todos os Comandos](#todos-os-comandos)
5. [Sistema de Divisões](#sistema-de-divisões)
6. [Fluxo de uma Partida](#fluxo-de-uma-partida)
7. [Sistema Duo](#sistema-duo)
8. [Blacklist](#blacklist)
9. [Configurações Avançadas](#configurações-avançadas)
10. [Solução de Problemas](#solução-de-problemas)

---

## Requisitos

- **Python 3.11+**
- Conta de desenvolvedor Discord (gratuita)
- Servidor Discord com cargo de admin

---

## Instalação

### 1. Clone / Baixe o projeto

```bash
# Baixe e extraia o ZIP, ou clone:
git clone <repo>
cd ar2bot
```

### 2. Instale as dependências

```bash
pip install -r requirements.txt
```

### 3. Crie o arquivo `.env`

```bash
cp .env.example .env
```

Edite o `.env` e coloque seu token:

```
DISCORD_TOKEN=SEU_TOKEN_AQUI
```

### 4. Configure o `config.py`

Abra `config.py` e preencha:

```python
# IDs dos canais do seu servidor
PARTIDAS_CHANNEL_ID   = 123456789   # canal onde partidas aparecem
LOG_CHANNEL_ID        = 987654321   # canal de logs
RESULTADOS_CHANNEL_ID = 111222333   # canal de resultados

# IDs dos cargos de admin e hoster
ADMIN_ROLE_IDS   = [444555666]
HOSTER_ROLE_IDS  = [777888999]
```

### 5. Inicie o bot

```bash
python main.py
```

> ✅ Na primeira vez, o bot vai criar o banco de dados e sincronizar os slash commands.
> Aguarde até 1 hora para os comandos aparecerem (ou use sync manual abaixo).

### Sync manual de comandos (opcional)

Se os comandos não aparecerem, adicione ao `on_ready`:
```python
await bot.tree.sync(guild=discord.Object(id=SEU_SERVER_ID))
```

---

## Configuração Inicial (após iniciar)

Execute no Discord como admin:

```
/config canal_partidas   #canal-partidas
/config canal_resultados #canal-resultados
/config canal_log        #canal-logs
/config trofeus vitoria:34 derrota:17
```

---

## Todos os Comandos

### 🎮 Partidas

| Comando | Descrição | Quem pode usar |
|---------|-----------|----------------|
| `/host [modo]` | Cria uma nova partida (1v1, 2v2, 3v3, 4v4, 5v5) | Todos |
| `/substituir [match_id] [substituto]` | Solicita substituição em partida ativa | Jogadores na partida |
| `/cancelar [match_id]` | Cancela uma partida | Admin/Hoster |
| `/resultado [match_id] [time]` | Força resultado de uma partida | Admin/Hoster |

### 📊 Ranking & Perfil

| Comando | Descrição | Quem pode usar |
|---------|-----------|----------------|
| `/perfil [membro]` | Exibe perfil ranqueado (troféus, divisão, winrate, histórico) | Todos |
| `/top [pagina]` | Ranking global dos melhores jogadores | Todos |
| `/historico [membro]` | Histórico das últimas 15 partidas | Todos |

### 👥 Duo

| Comando | Descrição |
|---------|-----------|
| `/duo` | Abre o painel de duo (ver/convidar/sair) |
| `/conviteduo` | Aceitar/recusar convite de duo pendente |

### 🛡️ Admin — Troféus

| Comando | Descrição |
|---------|-----------|
| `/addtrofeu [membro] [quantidade]` | Adiciona troféus manualmente |
| `/removertrofeu [membro] [quantidade]` | Remove troféus manualmente |
| `/settrofeu [membro] [quantidade]` | Define o valor exato de troféus |
| `/resetartemporada` | Zera todos os troféus (pede confirmação) |

### 🚫 Admin — Blacklist

| Comando | Descrição |
|---------|-----------|
| `/blacklist add [membro] [motivo]` | Bane jogador (não pode criar/entrar em partidas) |
| `/blacklist remove [membro]` | Remove jogador da blacklist |
| `/blacklist lista` | Lista todos os jogadores banidos |
| `/blacklist checar [membro]` | Verifica se um jogador está banido |

### ⚙️ Admin — Configurações

| Comando | Descrição |
|---------|-----------|
| `/config canal_partidas [canal]` | Define canal de partidas |
| `/config canal_resultados [canal]` | Define canal de resultados |
| `/config canal_log [canal]` | Define canal de logs |
| `/config trofeus [vitoria] [derrota]` | Define troféus por vitória/derrota |
| `/config ver` | Exibe configurações atuais |

---

## Sistema de Divisões

| Divisão | Troféus |
|---------|---------|
| 🥉 Bronze I | 0 – 99 |
| 🥉 Bronze II | 100 – 199 |
| 🥉 Bronze III | 200 – 299 |
| 🥈 Prata I | 300 – 399 |
| 🥈 Prata II | 400 – 499 |
| 🥈 Prata III | 500 – 599 |
| 🥇 Ouro I | 600 – 699 |
| 🥇 Ouro II | 700 – 799 |
| 🥇 Ouro III | 800 – 999 |
| 💎 Platina I | 1000 – 1199 |
| 💎 Platina II | 1200 – 1400 |
| 💎 Platina III | 1401 – 1600 |
| 🔷 Diamante I | 1601 – 1800 |
| 🔷 Diamante II | 1801 – 2000 |
| 🔷 Diamante III | 2001 – 2200 |
| 🔥 Ascendente I | 2201 – 2400 |
| 🔥 Ascendente II | 2401 – 2600 |
| 🔥 Ascendente III | 2601 – 2799 |
| 👑 Mestre | 2800+ |

---

## Fluxo de uma Partida

```
1. Jogador usa /host 3v3
   → Bot posta embed com botões no canal de partidas

2. Jogadores clicam em:
   🔴 Entrar — Time Vermelho
   🔵 Entrar — Time Azul
   👥 Entrar com Duo  (entra com seu duo registrado)

3. Quando o lobby encher (3 no vermelho + 3 no azul):
   → Status muda para "Em andamento"
   → Botões mudam para: 🔴 Time Vermelho Venceu / 🔵 Time Azul Venceu

4. Jogadores votam no resultado
   → Maioria simples decide o vencedor
   → Troféus são distribuídos automaticamente
   → Resultado postado no canal de resultados

Regras de troféus:
   ✅ Vitória: +34 troféus (configurável)
   ❌ Derrota:  -17 troféus (configurável, mínimo 0)
```

---

## Sistema Duo

1. Use `/duo` para abrir o painel
2. Selecione um amigo no menu para enviar convite
3. O amigo usa `/conviteduo` para aceitar
4. Ao entrar numa partida, clique em **👥 Entrar com Duo**
   - O bot adiciona ambos automaticamente no time com mais espaço

Para desfazer: `/duo` → **🚪 Sair do Duo**

---

## Blacklist

Jogadores na blacklist:
- Não podem criar partidas (`/host`)
- Não podem entrar em partidas (nem como duo)
- Continuam visíveis no ranking

```bash
/blacklist add @jogador Motivo aqui
/blacklist remove @jogador
/blacklist lista
/blacklist checar @jogador
```

---

## Configurações Avançadas

### Alterar troféus por partida

```
/config trofeus vitoria:50 derrota:25
```

### Personalizar divisões

Edite o array `DIVISIONS` no `config.py`:

```python
DIVISIONS = [
    (0,    99,   "Bronze I",  "🥉", 0xCD7F32),
    # (min, max, nome, emoji, cor_hex)
    ...
]
```

### Múltiplos servidores

O bot funciona em múltiplos servidores automaticamente.
Cada servidor tem configurações independentes (salvas no banco de dados).

### Banco de dados

O arquivo `ar2bot.db` (SQLite) é criado automaticamente.
Para backup: copie o arquivo `ar2bot.db`.

Tabelas:
- `players` — perfis e troféus
- `matches` — histórico de partidas
- `blacklist` — jogadores banidos
- `duos` — pares de duo
- `config` — configurações por servidor
- `history` — histórico detalhado de partidas

---

## Solução de Problemas

**Slash commands não aparecem**
→ Aguarde até 1 hora após o primeiro start
→ Ou use sync por servidor (mais rápido):
```python
# Em on_ready:
await bot.tree.sync(guild=discord.Object(id=SEU_SERVER_ID))
```

**Bot não responde**
→ Verifique se o TOKEN está correto no `.env`
→ Verifique se as intents estão ativas no Developer Portal:
  - `SERVER MEMBERS INTENT` ✅
  - `MESSAGE CONTENT INTENT` ✅

**Partida não aparece no canal certo**
→ Configure com `/config canal_partidas #canal`
→ Ou defina `PARTIDAS_CHANNEL_ID` no `config.py`

**Erro de permissão**
→ O bot precisa dos cargos: `Send Messages`, `Embed Links`, `Read Message History`, `Use Application Commands`

---

## 🛠️ Tecnologias

- **Python 3.11+**
- **discord.py 2.x** — biblioteca oficial
- **aiosqlite** — banco de dados assíncrono
- **SQLite** — armazenamento local

---

*Bot desenvolvido para AR2 Brasil — Apocalypse Rising 2*
