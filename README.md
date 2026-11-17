# CAB — Crew Agent Board

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
- **Transparente:** log ao vivo de cada agente, tokens, ferramentas usadas — e uma sala em pixel art com a equipe
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
    Senão, configure em **⚙ Configuração → Contas** (o app mostra o passo a passo e o comando pronto).
  - **Codex** (opcional): `codex login` ou chave de API, também em **Contas**.
- **GitHub CLI (`gh`) autenticado** — só se quiser que as entregas virem pull requests.

## Primeiros passos

1. Abra o CAB → **📁 Projetos → + Adicionar projeto** e escolha a pasta do seu repositório (ou **Novo projeto**).
2. Clique em **+ Pedido** e descreva o que quer, como descreveria para uma pessoa.
3. O planner devolve as tarefas em **Revisar definição**: leia, ajuste se quiser e **aprove**.
4. Acompanhe pelo quadro. Quando uma tarefa chegar em **Revisar resultado**, veja o diff e **aceite**, **corrija**
   (com um comentário — ela roda de novo) ou **reprove**.
5. Na aba **⚙ Projeto**, escolha a entrega: só no worktree, numa branch ou num **PR** (empilhados, na ordem das
   dependências).

**Dica:** o botão **Conversa** abre um chat com um agente que conhece o CAB — peça "cria um pedido pra X", "por que a
tarefa 01 não começa?" ou "monta o crew deste projeto".

## Outros jeitos de usar

- **Navegador:** com o app aberto, o quadro também fica em `http://127.0.0.1:4747`.
- **Claude Code / VS Code (MCP):**
  `claude mcp add --transport http crew http://127.0.0.1:4747/mcp` — veja o quadro, crie pedidos, responda e
  aprove sem sair do editor.
- **Telegram:** em **⚙ Configuração → Telegram**, ligue um bot e converse com o CAB pelo celular; ele avisa de
  perguntas, travamentos e resultados para aceitar.

## Personalizar

Agentes, perfis (o fluxo: quem planeja, quem executa, quem revisa) e plugins são arquivos, editáveis em
**⚙ Configuração** — inclusive com **Descrever com IA**. Valem para todos os projetos ou só para um. Dá para misturar
provedores (planner no Claude, executor no Codex), usar contas reserva quando uma bate no limite e integrar com o
Jira (embutido).

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
