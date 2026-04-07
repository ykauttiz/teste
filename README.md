# 🤠 AR2 Sheriff Bot — Guia Completo do Velho Oeste

Bot de duelos ranqueados no tema **Faroeste (Wild West)** para servidores Discord do jogo **Apocalypse Rising 2 (Roblox)**.
Sistema completo de matchmaking, troféus, divisões, parceiros de duelo e lista de procurados.

---

## 📋 Índice
1. [Visão Geral](#visão-geral)
2. [Requisitos](#requisitos)
3. [Instalação](#instalação)
4. [Configuração de Cargos](#configuração-de-cargos)
5. [Como Ser um Xerife (Hoster)](#como-ser-um-xerife-hoster)
6. [Todos os Comandos](#todos-os-comandos)
7. [Fluxo de um Duelo](#fluxo-de-um-duelo)
8. [Sistema de Divisões](#sistema-de-divisões)
9. [Sistema de Parceiros](#sistema-de-parceiros)
10. [Lista de Procurados (Blacklist)](#lista-de-procurados-blacklist)
11. [Configurações Avançadas](#configurações-avançadas)
12. [Solução de Problemas](#solução-de-problemas)

---

## Visão Geral

O **AR2 Sheriff Bot** gerencia duelos ranqueados no estilo Faroeste:

- 🤠 **Xerifes (Hosters)** criam duelos no saloon
- 🔫 **Pistoleiros (Jogadores)** entram nos bandos Vermelho ou Azul
- 🏆 **Sheriffs (Admins)** definem o resultado final dos tiroteios
- 🌵 Sistema de troféus para subir no ranking
- 👥 Sistema de parceiros para duelar em dupla

---

## Requisitos

- **Python 3.11+**
- Conta de desenvolvedor Discord (gratuita)
- Servidor Discord com cargos configurados

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

Abra `config.py` e preencha os IDs do seu servidor:

```python
# IDs dos canais do seu servidor
PARTIDAS_CHANNEL_ID   = 123456789   # canal onde duelos aparecem
LOG_CHANNEL_ID        = 987654321   # canal de logs internos
RESULTADOS_CHANNEL_ID = 111222333   # canal de resultados
RANKING_CHANNEL_ID    = 444555666   # canal do ranking ao vivo

# IDs dos cargos de admin e hoster
ADMIN_ROLE_IDS   = [444555666]   # Sheriffs - podem TUDO
HOSTER_ROLE_IDS  = [777888999]   # Xerifes - podem criar duelos
```

> ⚠️ **IMPORTANTE**: Os cargos devem ser configurados para o sistema funcionar!

### 5. Inicie o bot

```bash
python main.py
```

> ✅ Na primeira vez, o bot vai criar o banco de dados e sincronizar os slash commands.
> Aguarde até 1 hora para os comandos aparecerem em todos os servidores.

### Sync manual de comandos (opcional)

Se os comandos não aparecerem, adicione ao `on_ready`:
```python
await bot.tree.sync(guild=discord.Object(id=SEU_SERVER_ID))
```

---

## Configuração de Cargos

O bot usa uma hierarquia de permissões no estilo Faroeste:

| Cargo | ID no Config | Permissões |
|-------|--------------|------------|
| **🤠 Sheriff (Admin)** | `ADMIN_ROLE_IDS` | Pode tudo: criar duelos, definir resultados, cancelar, configurar bot |
| **⭐ Xerife (Hoster)** | `HOSTER_ROLE_IDS` | Pode criar duelos e cancelar. **NÃO** pode definir resultado |
| **🔫 Pistoleiro (Jogador)** | Nenhum | Pode entrar em duelos, ver perfil/ranking, usar parceiros |

### Como obter os IDs dos cargos:

1. No Discord, ative o modo desenvolvedor (Configurações → Avançado)
2. Clique com botão direito no cargo → "Copiar ID do Cargo"
3. Cole no `config.py`

---

## Como Ser um Xerife (Hoster)

Os **Xerifes** são responsáveis por criar e organizar os duelos ranqueados do servidor.

### 🎮 O que faz um Xerife

- Criar salas privadas para os duelos no saloon
- Organizar os pistoleiros nos bandos
- Garantir que as regras dos duelos sejam seguidas
- Iniciar e acompanhar o andamento dos tiroteios
- Cancelar duelos quando necessário

### 📝 Como se tornar um Xerife

1. Abra um **ticket** no canal de suporte do servidor
2. Informe que você tem interesse em se tornar **Xerife (Hoster)**
3. Converse com um **Sheriff (Admin) ou moderador**
4. A equipe irá avaliar seu pedido

> 🔧 No futuro, pretendemos organizar melhor esse processo através de **formulários de inscrição**.

---

## Todos os Comandos

### 🤠 Duelos

| Comando | Descrição | Quem pode usar |
|---------|-----------|----------------|
| `/host [modo]` | Cria um novo duelo (1v1, 2v2, 3v3, 4v4, 5v5) | Apenas Xerifes (Hosters) |
| `/substituir [match_id] [substituto]` | Solicita substituição em duelo ativo | Pistoleiros no duelo |
| `/cancelar [match_id]` | Cancela um duelo | Staff (Hosters + Admins) |
| `/resultado [match_id] [bando]` | Define o resultado final do duelo | Apenas Sheriffs (Admins) |

### 📊 Ranking & Perfil

| Comando | Descrição | Quem pode usar |
|---------|-----------|----------------|
| `/perfil [membro]` | Exibe perfil ranqueado (troféus, divisão, winrate, histórico) | Todos |
| `/top [pagina]` | Ranking global dos melhores pistoleiros | Todos |
| `/historico [membro]` | Histórico das últimas 15 partidas | Todos |

### 👥 Parceiros de Duelo

| Comando | Descrição |
|---------|-----------|
| `/duo` | Abre o painel de parceiro (ver/convidar/sair) |
| `/conviteduo` | Aceitar/recusar convite de parceiro pendente |

### 🛡️ Admin — Troféus

| Comando | Descrição |
|---------|-----------|
| `/addtrofeu [membro] [quantidade]` | Adiciona troféus manualmente |
| `/removertrofeu [membro] [quantidade]` | Remove troféus manualmente |
| `/settrofeu [membro] [quantidade]` | Define o valor exato de troféus |
| `/resetartemporada` | Zera todos os troféus (pede confirmação) |

### 🚫 Admin — Lista de Procurados

| Comando | Descrição |
|---------|-----------|
| `/blacklist add [membro] [motivo]` | Bane pistoleiro (não pode criar/entrar em duelos) |
| `/blacklist remove [membro]` | Remove pistoleiro da lista de procurados |
| `/blacklist lista` | Lista todos os pistoleiros procurados |
| `/blacklist checar [membro]` | Verifica se um pistoleiro está procurado |

### ⚙️ Admin — Configurações

| Comando | Descrição |
|---------|-----------|
| `/config canal_partidas [canal]` | Define canal de duelos |
| `/config canal_resultados [canal]` | Define canal de resultados |
| `/config canal_log [canal]` | Define canal de logs |
| `/config trofeus [vitoria] [derrota]` | Define troféus por vitória/derrota |
| `/config ver` | Exibe configurações atuais |

---

## Fluxo de um Duelo

```
1. Xerife usa /host 3v3
   → Bot posta embed com botões no canal de duelos (saloon)
   
   🎯 Duelo no Saloon #ABC123
   ━━━━━━━━━━━━━━━━━━━━━
   🔴 Bando Vermelho: Vazio
   🔵 Bando Azul: Vazio
   
   [🔴 Entrar — Bando Vermelho] [🔵 Entrar — Bando Azul]
   [🤠 Entrar com Parceiro] [❌ Cancelar Duelo]

2. Pistoleiros clicam em:
   🔴 Entrar — Bando Vermelho
   🔵 Entrar — Bando Azul
   🤠 Entrar com Parceiro (entra com seu parceiro registrado)

3. Quando o saloon lotar (3 no vermelho + 3 no azul):
   → Status muda para "Em andamento"
   → Botão muda para: 🏆 Aguardando Sheriff (Admin)
   
4. Sheriff (Admin) usa /resultado para definir o vencedor
   → Troféus são distribuídos automaticamente
   → Resultado postado no canal de resultados

Regras de troféus:
   ✅ Vitória: +34 troféus (configurável)
   ❌ Derrota: -17 troféus (configurável, mínimo 0)
   
⚠️ Bônus: Duelos 4v4 e 5v5 concedem +20% de troféus!
```

### ⚠️ Regras Importantes dos Duelos

- Respeite os pistoleiros e xerifes durante os tiroteios
- Não abandone duelos em andamento
- Rejoin apenas no primeiro e segundo round
- Siga todas as regras do servidor

---

## Sistema de Divisões

Sua divisão é baseada nos troféus conquistados em duelos:

| Divisão | Troféus | Emoji |
|---------|---------|-------|
| 🥉 Bronze I | 0 – 99 | 🥉 |
| 🥉 Bronze II | 100 – 199 | 🥉 |
| 🥉 Bronze III | 200 – 299 | 🥉 |
| 🥈 Prata I | 300 – 399 | 🥈 |
| 🥈 Prata II | 400 – 499 | 🥈 |
| 🥈 Prata III | 500 – 599 | 🥈 |
| 🥇 Ouro I | 600 – 699 | 🥇 |
| 🥇 Ouro II | 700 – 799 | 🥇 |
| 🥇 Ouro III | 800 – 999 | 🥇 |
| 💎 Platina I | 1000 – 1199 | 💎 |
| 💎 Platina II | 1200 – 1400 | 💎 |
| 💎 Platina III | 1401 – 1600 | 💎 |
| 🔷 Diamante I | 1601 – 1800 | 🔷 |
| 🔷 Diamante II | 1801 – 2000 | 🔷 |
| 🔷 Diamante III | 2001 – 2200 | 🔷 |
| 🔥 Ascendente I | 2201 – 2400 | 🔥 |
| 🔥 Ascendente II | 2401 – 2600 | 🔥 |
| 🔥 Ascendente III | 2601 – 2799 | 🔥 |
| 👑 Mestre | 2800+ | 👑 |

---

## Sistema de Parceiros

1. Use `/duo` para abrir o painel
2. Selecione um amigo no menu para enviar convite
3. O amigo usa `/conviteduo` para aceitar
4. Ao entrar num duelo, clique em **🤠 Entrar com Parceiro**
   - O bot adiciona ambos automaticamente no bando com mais espaço

Para desfazer: `/duo` → **🚪 Sair do Duo**

---

## Lista de Procurados (Blacklist)

Pistoleiros na lista de procurados:
- 🚫 Não podem criar duelos (`/host`)
- 🚫 Não podem entrar em duelos (nem como parceiro)
- ✅ Continuam visíveis no ranking

### Comandos de Procurados:

```bash
/blacklist add @jogador Motivo aqui
/blacklist remove @jogador
/blacklist lista
/blacklist checar @jogador
```

---

## Configurações Avançadas

### Alterar troféus por duelo

```
/config trofeus vitoria:50 derrota:25
```

### Configuração Inicial (após iniciar o bot)

Execute no Discord como Sheriff (Admin):

```
/config canal_partidas   #saloon-duelos
/config canal_resultados #resultados-duelos
/config canal_log        #logs-xerifes
/config trofeus vitoria:34 derrota:17
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
- `players` — perfis e troféus dos pistoleiros
- `matches` — histórico de duelos
- `blacklist` — pistoleiros procurados
- `duos` — pares de parceiros
- `config` — configurações por servidor
- `history` — histórico detalhado de duelos

---

## Solução de Problemas

### Slash commands não aparecem
→ Aguarde até 1 hora após o primeiro start
→ Ou use sync por servidor (mais rápido):
```python
# Em on_ready:
await bot.tree.sync(guild=discord.Object(id=SEU_SERVER_ID))
```

### Bot não responde
→ Verifique se o TOKEN está correto no `.env`
→ Verifique se as intents estão ativas no Developer Portal:
  - `SERVER MEMBERS INTENT` ✅
  - `MESSAGE CONTENT INTENT` ✅

### Duelo não aparece no canal certo
→ Configure com `/config canal_partidas #canal`
→ Ou defina `PARTIDAS_CHANNEL_ID` no `config.py`

### Erro de permissão
→ O bot precisa das permissões:
  - `Send Messages` ✅
  - `Embed Links` ✅
  - `Read Message History` ✅
  - `Use Application Commands` ✅

### "Apenas Xerifes podem criar duelos"
→ Verifique se o cargo do usuário está em `HOSTER_ROLE_IDS` no `config.py`
→ O bot precisa ver o cargo do membro (coloque o cargo do bot acima dos outros)

### "Apenas Sheriffs podem definir resultado"
→ Verifique se o cargo do admin está em `ADMIN_ROLE_IDS` (não em HOSTER_ROLE_IDS)
→ Hosters podem criar duelos mas **não** podem definir resultado

---

## 🛠️ Tecnologias

- **Python 3.11+**
- **discord.py 2.x** — biblioteca oficial
- **aiosqlite** — banco de dados assíncrono
- **SQLite** — armazenamento local

---

## 📝 Notas da Versão

### v2.0 — Temática Faroeste
- 🤠 Temática Wild West completa
- 🎯 Apenas Hosters podem criar duelos
- 🏆 Apenas Admins podem definir resultados
- 🔫 Sistema de votação removido (agora apenas admins definem resultado)
- 🌵 Mensagens temáticas em todos os comandos

---

*Bot desenvolvido para AR2 Brasil — Apocalypse Rising 2*

**Boa sorte nos duelos e boa escalada no ranking!** 🏆🔥🤠
