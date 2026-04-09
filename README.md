<div align="center">

# 🤠 AR2 Sheriff Bot

### *Sistema de Duelos Ranqueados para Apocalypse Rising 2*

[![Discord](https://img.shields.io/badge/Discord-Servidor%20do%20Dono-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/Hy68Ht6St6)
[![Roblox](https://img.shields.io/badge/Roblox-AR2-000000?style=for-the-badge&logo=roblox&logoColor=white)](https://roblox.com)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)

</div>

---

## 📝 O que é o AR2 Sheriff Bot?

Bot de Discord profissional para gerenciamento de **duelos ranqueados** no estilo **Faroeste (Wild West)** para jogadores de **Apocalypse Rising 2 (Roblox)**.

### Funcionalidades Principais:

- � **Sistema de Duelos** - Criação de partidas 1v1 até 5v5
- 🏆 **Ranking Ranqueado** - Sistema de troféus e divisões
- 🚫 **Blacklist Profissional** - 30+ códigos de banimento
- 📊 **Estatísticas** - Perfil completo de cada jogador
- 🖼️ **Imagens Customizáveis** - 20+ slots de imagens configuráveis

---

## � Índice

- [Instalação](#-instalação)
- [Configuração](#-configuração)
- [Comandos](#-comandos)
- [Blacklist](#-sistema-de-blacklist)
- [Divisões](#-sistema-de-divisões)

---

## 🚀 Instalação

### 1. Instale as dependências:
```bash
pip install -r requirements.txt
```

### 2. Configure o arquivo `.env`:
```bash
cp .env.example .env
```

### 3. Preencha seu `.env`:
```env
DISCORD_TOKEN=seu_token_aqui

# Canais
PARTIDAS_CHANNEL_ID=123456789
RESULTADOS_CHANNEL_ID=123456789
RANKING_CHANNEL_ID=123456789
BLACKLIST_CHANNEL_ID=123456789

# Cargos
ADMIN_ROLE_IDS=123456789
HOSTER_ROLE_IDS=123456789

# Opcional: Imagens
BOT_IMAGE_URL=https://seu-site.com/imagem.png
FOOTER_IMAGE_URL=https://seu-site.com/icon.png
```

### 4. Execute:
```bash
python main.py
```

---

## ⚙️ Configuração

### Canais Necessários:
| Canal | Descrição | Comando |
|-------|-----------|---------|
| #saloon | Criação de duelos | `/config canal_partidas` |
| #resultados | Resultados | `/config canal_resultados` |
| #blacklist | Logs de ban | `/config canal_blacklist` |
| #ranking | Hall da Fama | `/config canal_ranking` |

### Cargos:
| Cargo | Permissões |
|-------|-----------|
| 🏆 Sheriff (Admin) | Todos os comandos |
| 🤠 Hoster | Criar/cancelar duelos |
| 🔫 Pistoleiro | Jogar em duelos |

---

## 🚫 Sistema de Blacklist

### Códigos de Banimento (30+):

**🎯 Exploits (Permanente):**
- `EXP001` - Aimbot / ESP
- `EXP002` - Speed Hack
- `EXP003` - God Mode
- `EXP004` - Infinite Ammo
- `EXP005` - Duplicação
- `EXP006` - Teleport
- `EXP007` - Admin Abuse

**🔍 Screenshare:**
- `SS001` - Cheats Encontrados
- `SS002` - Serviços Desabilitados
- `SS003` - Histórico Suspeito
- `SS004` - Recusa em SS
- `SS005` - Limpeza de Logs
- `SS006` - Overlays Ocultos
- `SS007` - Macros

**🔧 Hardware:**
- `HW001` - HWID Ban
- `HW002` - Serial Ban
- `HW003` - IP/VPN

**🤬 Toxicidade:**
- `TOX001` - Comportamento Tóxico
- `TOX002` - Racismo/Discurso de Ódio
- `TOX003` - Doxxing
- `TOX004` - Ear Rape
- `TOX005` - Spam

**⚔️ Game Abuse:**
- `ABU001` - Combat Logging
- `ABU002` - Team Killing
- `ABU003` - Spawn Killing
- `ABU004` - Griefing
- `ABU005` - RDM

### Comandos Blacklist:
```
/blacklist add @usuario EXP001
/blacklist add @usuario SS001 duracao_dias:7
/blacklist remove @usuario "Cumpriu pena"
/blacklist motivos      # Lista todos
/blacklist lista         # Ver procurados
/blacklist checar @user  # Verificar status
```

---

## ⌨️ Comandos

### 🎮 Duelos
| Comando | Descrição | Permissão |
|---------|-----------|-----------|
| `/host` | Criar duelo | Hoster+ |
| `/cancelar` | Cancelar duelo | Hoster+ |
| `/substituir` | Trocar jogador | Hoster+ |
| `/resultado` | Definir vencedor | Hoster+ |

### 🏆 Ranking
| Comando | Descrição |
|---------|-----------|
| `/perfil` | Seu perfil |
| `/stats @user` | Perfil de outro |
| `/top` | Top 10 global |
| `/historico` | Suas partidas |

### 🚫 Blacklist (Admin)
| Comando | Descrição |
|---------|-----------|
| `/blacklist add` | Banir usuário |
| `/blacklist remove` | Desbanir |
| `/blacklist motivos` | Ver códigos |
| `/blacklist lista` | Lista de bans |

### ⚙️ Admin
| Comando | Descrição |
|---------|-----------|
| `/addtrofeu @user 100` | Dar troféus |
| `/removertrofeu @user 50` | Remover troféus |
| `/config canal_partidas #canal` | Setar canal |
| `/config trofeus 34 17` | Configurar troféus |
| `/resetartemporada` | Resetar temporada |

---

## 🎖️ Sistema de Divisões

| Divisão | Troféus | Emoji |
|---------|---------|-------|
| Bronze I-III | 0-299 | 🥉 |
| Prata I-III | 300-599 | 🥈 |
| Ouro I-III | 600-999 | 🥇 |
| Platina I-III | 1000-1599 | 💎 |
| Diamante I-III | 1600-2199 | 🔷 |
| Ascendente I-III | 2200-2799 | 🔥 |
| **Mestre** | 2800+ | 👑 |

---

## �️ Sistema de Imagens

O bot suporta **20+ imagens configuráveis** via `.env`:

```env
# Principais
BOT_IMAGE_URL=           # Imagem padrão
FOOTER_IMAGE_URL=        # Ícone do rodapé
THUMBNAIL_URL=           # Thumbnail padrão

# Por tipo
LOBBY_IMAGE_URL=         # Lobby
RESULT_IMAGE_URL=        # Resultados
RANKING_IMAGE_URL=       # Hall da Fama
PROFILE_IMAGE_URL=       # Perfil
BLACKLIST_IMAGE_URL=     # Blacklist
BAN_IMAGE_URL=           # Banimento
SUCCESS_IMAGE_URL=       # Sucesso
ERROR_IMAGE_URL=         # Erro
WARNING_IMAGE_URL=       # Aviso
INFO_IMAGE_URL=          # Info
```

> **Nota:** Deixe vazio para não usar imagens. O bot funciona perfeitamente sem!

---

## 📞 Suporte

**Discord do Dono:** [discord.gg/Hy68Ht6St6](https://discord.gg/Hy68Ht6St6)

---

<div align="center">

**🤠 AR2 Sheriff Bot**  
*Sistema Profissional de Duelos*

</div>
- 📊 **Ranking Ao Vivo** - Top 100 atualizado automaticamente
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
| `/stats [membro]` | 📊 Estatísticas detalhadas do pistoleiro | Todos |

### 🏆 Ranking Ao Vivo

| Comando | Descrição | Quem pode usar |
|---------|-----------|----------------|
| `/rankingconfig [canal] [intervalo]` | Configura ranking ao vivo | Apenas Sheriffs (Admins) |
| `/forcarank` | Força atualização imediata do ranking | Apenas Sheriffs (Admins) |

**Como funciona o Ranking Ao Vivo:**
- 🔄 Atualiza automaticamente a cada 30 minutos (configurável)
- 📌 Mensagem fixada no canal de ranking
- 📊 Mostra Top 100 com paginação por botões
- 📈 Estatísticas do servidor (total de pistoleiros, troféus)

### �� Parceiros de Duelo

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
| `/rankingconfig [canal] [intervalo]` | 🏆 Configura ranking ao vivo (padrão: 30 min) |
| `/forcarank` | 🔄 Força atualização imediata do ranking |

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

🔔 **Notificações:**
- **@everyone** é mencionado automaticamente quando um novo duelo é criado
- A notificação se auto-deleta após 5 minutos para não poluir o canal
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
/rankingconfig #ranking-ao-vivo 30
```

> 🏆 O ranking ao vivo atualiza automaticamente a cada 30 minutos e notifica @everyone de mudanças importantes!

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

## Sistema de Correção Automática de Problemas

O bot possui um **sistema de monitoramento** que verifica duelos a cada minuto e corrige problemas automaticamente:

### Timeouts Automáticos

| Situação | Timeout | Aviso Prévio |
|----------|---------|--------------|
| Duelo esperando jogadores | 30 minutos | 10 min antes |
| Duelo em andamento sem resultado | 90 minutos | 10 min antes |

**O que acontece:**
- Aviso é enviado no canal 10 minutos antes do timeout
- Duelo é cancelado automaticamente após o timeout
- Motivo é registrado nos logs

### Jogadores que Saem do Servidor

**Detecção:** Sistema verifica a cada minuto se algum jogador saiu do servidor durante um duelo ativo.

**Ação automática:**
- Jogador é removido do duelo imediatamente
- Notificação é enviada no canal (auto-deleta em 1 min)
- Se todos saírem, o duelo é cancelado

### Prevenção de Problemas

**Proteções implementadas:**

1. **Anti-Duplicata:**
   - Jogador não pode entrar em múltiplos duelos simultâneos
   - Xerife não pode criar novo duelo se já estiver em um

2. **Anti-Spam:**
   - Xerifes blacklistados não podem criar duelos
   - Cooldown implícito entre criações

3. **Proteção de Canal:**
   - Se canal de duelo for deletado, duelo é cancelado automaticamente
   - Mensagens órfãs são detectadas e limpas

4. **Persistência de Dados:**
   - Banco de dados SQLite salva automaticamente
   - Troféus e histórico são preservados mesmo se bot reiniciar
   - Duelos em andamento persistem entre reinicializações

### Comandos de Emergência

**Para Admins (Sheriffs):**

```bash
/cancelar [match_id]     # Cancela qualquer duelo imediatamente
/forcarank               # Força atualização do ranking
```

**Problemas comuns e soluções:**

| Problema | Causa | Solução |
|----------|-------|---------|
| "Duelo não aparece" | Canal incorreto | Configure com `/config canal_partidas` |
| "Não consigo entrar" | Já está em outro duelo | Saia do duelo atual ou espere terminar |
| "Botão não funciona" | Duelo expirou | Duelo foi cancelado por timeout |
| "Resultado não registra" | Apenas admins podem | Peça a um Sheriff (Admin) |
| "Perdi troféus sem jogar" | Saiu do servidor | Sistema detectou e aplicou regras |

### Logs e Monitoramento

O bot registra automaticamente:
- Criação e cancelamento de duelos
- Entradas e saídas de jogadores
- Definição de resultados
- Timeouts e cancelamentos automáticos
- Erros e exceções

**Canal de logs:** Configure com `/config canal_log #canal`

---

## Tecnologias

- **Python 3.11+**
- **discord.py 2.x** — biblioteca oficial
- **aiosqlite** — banco de dados assíncrono
- **SQLite** — armazenamento local

---

## Notas da Versão

### v2.2 — Sistema de Correção de Problemas
- Timeouts automáticos para duelos inativos
- Detecção automática de jogadores que saíram
- Proteção anti-duplicata de duelos
- Limpeza automática de duelos órfãos
- Sistema de avisos antes de timeouts

### v2.1 — Ranking Ao Vivo & Estatísticas Avançadas
- Sistema de Ranking Ao Vivo (atualiza a cada 30 min)
- Comando `/stats` com análise detalhada
- Notificações @everyone para novos duelos
- Estatísticas de horário de pico
- Análise de volatilidade e consistência

### v2.0 — Temática Faroeste
- Temática Wild West completa
- Apenas Hosters podem criar duelos
- Apenas Admins podem definir resultados
- Sistema de votação removido
- Mensagens temáticas em todos os comandos

---

*Bot desenvolvido para AR2 Brasil — Apocalypse Rising 2*

**Boa sorte nos duelos e boa escalada no ranking!** 
