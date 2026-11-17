# CAB — Crew Agent Board

[English](README.md) · **Português**

**Um quadro kanban local onde agentes de IA fazem o trabalho e você toma as decisões.**

Você escreve um **pedido** ("login com e-mail", "corrigir o relatório de vendas"). O CAB quebra em **tarefas** com
dependências, cada tarefa roda num agente (Claude ou Codex) numa cópia isolada do seu repositório, passa por um
agente revisor e volta para você aceitar. **Nada chega ao seu código sem o seu aceite.**

> Este repositório guarda só os instaladores e as atualizações do app. Baixe em
> **[Releases → última versão](https://github.com/sargita92/cab-releases/releases/latest)**.

## Como funciona

```
pedido ──► planner quebra em tarefas ──► você aprova a definição
                                              │
          ┌───────────────────────────────────┘
          ▼
  agente executa (worktree próprio) ──► reviewer revisa ──► você aceita, corrige ou reprova
                                                                    │
                                                    entrega: nada · branch · PR
```

- **Cada tarefa num worktree** (branch `crew/<pedido>/<tarefa>`): o seu checkout não é tocado.
- **Perguntas (❓):** quando o agente tem dúvida, a triagem responde o que já está no pedido ou no código; o resto vem
  para você.
- **Travou (⛔):** erro, limite, conflito — aparece no card com o motivo e o botão Retomar.
- **Em paralelo:** várias tarefas ao mesmo tempo, respeitando as dependências (🔗).
- **Transparente:** log ao vivo de cada agente, tokens e ferramentas usadas — e uma sala em pixel art com a equipe
  trabalhando.

## Instalar

| Sistema | Arquivo | Observação |
|---|---|---|
| **Windows** | `CAB-x.y.z-setup.exe` | Instala no seu usuário, sem admin. O SmartScreen avisa porque o app não é assinado: **Mais informações → Executar assim mesmo**. |
| **Linux / WSL** | `CAB-x.y.z.AppImage` | `chmod +x CAB-*.AppImage` e rode. É o formato que se atualiza sozinho. |
| **Linux / WSL (.deb)** | `CAB-x.y.z.deb` | `sudo apt install ./CAB-*.deb`. No WSL, o CAB aparece no menu Iniciar do Windows. |
| **Mac (Apple Silicon)** | `CAB-x.y.z-arm64.dmg` | Sem assinatura da Apple: depois de arrastar para Aplicativos, rode `xattr -cr /Applications/CAB.app` uma vez (senão o Mac diz que "está danificado"). |

### O que precisa ter na máquina

- **git** — os projetos são repositórios git.
- **Uma conta de IA** — o Claude já vem dentro do app:
  - **Claude:** assinatura (Pro/Max) ou chave de API. Se você já usa o Claude Code nesta máquina, o login dele vale.
    Senão, configure em **⚙ → Contas** (o app mostra o passo a passo e o comando pronto).
  - **Codex** (opcional): `codex login` ou chave de API, também em **Contas**.
- **GitHub CLI (`gh`) autenticado** — só se quiser que as entregas virem pull requests.

## Primeiros passos

1. Abra o CAB → **📁 Projetos → + Adicionar projeto** e escolha a pasta do seu repositório (ou **Novo projeto**).
2. Clique em **+ Pedido** e descreva o que quer, como descreveria para uma pessoa.
3. O planner devolve as tarefas em **Revisar definição**: leia, ajuste se quiser e **aprove**.
4. Acompanhe pelo quadro. Quando uma tarefa chegar em **Revisar resultado**, veja o diff e **aceite**, **corrija**
   (com um comentário — ela roda de novo) ou **reprove**.
5. Em **⚙ → Projeto**, escolha a entrega: só no worktree, numa branch ou num **PR** (empilhados, na ordem das
   dependências).

## Conversa — o chat que conhece o projeto

O botão **Conversa** abre um chat com um agente que roda **dentro do seu repositório** e **opera o CAB**. Ele sabe
tudo do projeto:

- **O código:** lê, busca, roda comandos e testes, mexe em arquivos — como o Claude Code no terminal.
- **O quadro:** pedidos, tarefas, status, logs de cada agente, perguntas pendentes, por que algo travou.
- **O próprio CAB:** traz o manual embutido (formatos, perfis, receitas) e a configuração do projeto.

Por isso dá para pedir em linguagem natural:

- "cria um pedido pra adicionar exportação em CSV"
- "por que a tarefa 01 não começa?" · "o que o executor fez na tarefa 03?"
- "destrava a tarefa 02" · "aceita a 01" (aceitar só quando você pede explicitamente)
- "sempre passe pelo agente de QA antes do reviewer" — ele ajusta o perfil
- "monta o crew deste projeto" — cria agentes e fluxo sob medida para um repositório novo
- "cria um plugin para o Trello"

Cada mensagem pode escolher **modelo**, **effort**, modo **Plan** (só propõe, não edita) e **MCPs globais** (usa os
MCPs que você instalou no Claude Code). Aceita anexos (📎, Ctrl+V, arrastar). Projeto vindo de outra ferramenta com
agentes próprios (`.claude/agents/`)? **Portar para o CAB** abre uma conversa que converte agentes, fluxo e backlog.

## Agentes

Cada etapa do fluxo é um agente — um arquivo com instruções, ferramentas, modelo e limites.

| Agente | O que faz |
|---|---|
| `planner` | Quebra o pedido em tarefas com dependências |
| `executor` | Faz a tarefa no worktree |
| `reviewer` | Revisa o resultado antes de chegar em você (reprova → volta para o executor) |
| `triage` | Responde perguntas do agente que já têm resposta no pedido/código; decide se uma tarefa longa continua ou travou |
| `fixer` | Tenta consertar um ⛔ antes de chamar você (opcional) |
| `conflict_resolver` | Resolve conflitos de merge entre tarefas |
| `pr_publisher` | Abre o PR quando a tarefa chega em revisão (opcional) |
| `chat`, `telegram`, `porter` | A Conversa, o bot do Telegram e o portador de projetos |
| `agent_writer`, `claudemd_writer` | Criam agentes e o `CLAUDE.md` do repositório a partir de texto |

Os embutidos são genéricos. O poder está em **criar os seus** — `dev-backend`, `qa`, `designer`, `rpa`… — e um
**perfil** que os encadeie (ex.: `planner → dev-backend → qa → reviewer`). Cada agente pode usar um provedor
diferente: planner no Claude, executor no Codex.

Na tarefa, a aba **Agentes** mostra cada execução (tempo, tokens, ferramentas). O botão 🏢 abre a **sala dos
agentes**: pixel art com a equipe sentada digitando enquanto trabalha e tomando café quando não.

## Telegram

Converse com o CAB pelo celular.

1. Crie um bot com o **@BotFather** e copie o token.
2. Em **⚙ → Telegram**, cole o token, ligue e salve.
3. Mande uma mensagem ao bot: ele mostra o seu id. Preencha e salve — só você fala com ele.

Daí em diante você recebe aviso de **pergunta (❓)**, **travamento (⛔)**, **definição para aprovar** e **resultado
para aceitar**. Responder a um aviso já diz de qual tarefa você está falando: "use JWT", "aprova", "corrige: falta o
teste". Também entende linguagem natural ("como está o quadro?", "cria um pedido pra…"). `/novo` começa outra
conversa.

## Configuração (⚙)

Tudo é arquivo, em camadas — a mais específica vence: **embutido** → **Meu** (vale para todos os projetos,
`~/.crew/`) → **Só este projeto** (`<repo>/.crew/`, pode ir para o git com o time). Editar um embutido grava uma
cópia sua; o original nunca muda.

| Aba | Para quê |
|---|---|
| **Agentes** | Criar e editar agentes em formulário ou no arquivo; **Descrever com IA** cria a partir de texto |
| **Perfis** | O fluxo: quem planeja, executa, revisa, tria, conserta e entrega |
| **Projeto** | Perfil do projeto, entrega (nada/branch/PR), tarefas em paralelo, modo de execução, gerar o `CLAUDE.md` |
| **Ondas** | Plano por ondas: a próxima só começa quando a atual é aceita (aparece no modo Ondas) |
| **Plugins** | Integrações (o **Jira** vem embutido: puxa cards como pedidos e move o card conforme a tarefa anda) |
| **Geral** | Provedor padrão (Claude/Codex), limite de anexos, arquivar concluídos, opções de tela |
| **Contas** | Contas pessoal × empresa por projeto; **contas reserva** assumem quando uma bate no limite de uso |
| **Telegram** | O bot (acima) |

**Modos de execução:** **clássico** (cada vaga leva a tarefa até o fim), **esteira** (cada agente é um posto com
pilha: o executor passa ao reviewer e já pega a próxima) e **Ondas**. **⏸ Pausar tudo** para todos os agentes na
hora; **▶ Retomar** continua de onde parou.

## Outros jeitos de usar

- **Navegador:** com o app aberto, o quadro também fica em `http://127.0.0.1:4747`.
- **Claude Code / VS Code (MCP):**
  `claude mcp add --transport http crew http://127.0.0.1:4747/mcp` — veja o quadro, crie pedidos, responda e
  aprove sem sair do editor.

## Atualizações

O app procura versão nova ao abrir e a cada 6 horas.

- **Windows e AppImage:** baixa sozinho e pergunta **Reiniciar?**; se você deixar para depois, instala ao sair do app.
  Agentes rodando no momento ficam ⛔ interrompidos e voltam com **Retomar**.
- **Mac e .deb:** avisa da versão nova e abre esta página para você baixar.

## Problemas comuns

- **Porta 4747 ocupada:** o app usa outra porta livre sozinho.
- **App no Windows e CAB no WSL na mesma máquina:** quem abre primeiro fica com o `localhost:4747`. Use um dos dois
  por vez.
- **Emoji como quadrado no Linux/WSL:** `sudo apt install fonts-noto-color-emoji`.
- **Rodar tarefas no Windows nativo** ainda não foi validado; no WSL e no Linux está bem testado.

---

© SArgita · [AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.html)
