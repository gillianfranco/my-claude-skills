# Obsidian Study Notes Organizer

Audita e melhora os links internos de um vault Obsidian completo (`.md`) usando o Obsidian CLI (com fallback para Python quando o CLI não estiver disponível).

## O que faz

Executa um fluxo de **5 fases** cobrindo o vault inteiro, do nível de cada matéria até a estrutura global:

- **Fase 1 — Auditoria intra-pasta**: organiza a seção `### Related:` de cada nota com links para o arquivo pai e relações conceituais dentro da mesma matéria
- **Fase 2 — Links quebrados**: remove da seção Related qualquer link que aponte para arquivo inexistente no vault
- **Fase 3 — Cross-folder linking**: identifica e adiciona relações genuinamente interdisciplinares entre matérias diferentes
- **Fase 4 — Vault Index**: cria ou atualiza o arquivo índice central do vault listando todas as matérias
- **Fase 5 — Vault Parent Link**: garante que todas as notas do vault tenham link para o arquivo índice central

A skill aplica **skip inteligente**: antes de editar qualquer arquivo, verifica se a edição é necessária. Arquivos já corretos são pulados.

## Modo de operação

A skill opera em dois modos:

- **Obsidian CLI** (preferencial): usa comandos como `obsidian read`, `obsidian update`, `obsidian backlinks` e `obsidian append`
- **Python** (fallback): quando o CLI não está disponível, usa leitura/escrita direta de arquivos `.md`

A detecção é automática — a skill verifica a disponibilidade do CLI antes de começar.

## Estrutura de vault esperada

```
Computação/                        ← raiz do vault
├── Computação.md                  ← arquivo índice central do vault
├── Programação/
│   ├── Programação.md             ← arquivo pai da matéria
│   └── Notes/
│       ├── 202501011000 - Nota A.md
│       └── 202501011010 - Nota B.md
├── Database/
│   ├── Database.md
│   └── Notes/
│       └── ...
└── PHP/
    ├── PHP.md
    └── Notes/
        └── ...
```

## Como usar

Dentro do Claude Code, mencione a tarefa em linguagem natural. A skill é ativada automaticamente por palavras-chave como "Related", "links entre notas", "arquivo pai", "notas do Obsidian", "auditoria de links", "organizar notas", "relações entre matérias", "vault inteiro", "índice do vault", "links quebrados" ou "links inativos".

**Exemplos de invocação:**

```
"Audite os links de todo o meu vault de Computação"
"Organize os links entre todas as minhas notas do Obsidian"
"Remova links quebrados das minhas notas"
"Crie o arquivo índice central do vault"
"Garanta que todas as notas tenham o link do vault"
```

## O que a skill pergunta

Ao ser invocada, a skill coleta o que ainda não foi mencionado na conversa:

1. **Nome do vault** — ex: `Computação`
2. **Caminho da raiz** — caminho completo da pasta raiz do vault no sistema de arquivos
3. **Links inline?** — além da seção Related, ajustar links dentro do corpo do texto também (opcional)

## Formato de saída

Ao final, a skill apresenta um relatório consolidado por fase:

- **Fase 1**: notas verificadas / editadas / puladas; links intra-pasta adicionados
- **Fase 2**: total de links quebrados removidos e detalhamento por nota
- **Fase 3**: novas relações interdisciplinares adicionadas; links cross-folder obsoletos removidos
- **Fase 4**: se o índice foi criado / atualizado / já estava correto; matérias listadas
- **Fase 5**: notas com link já presente (puladas) vs. com link adicionado
- **Erros e avisos**: arquivos ignorados, pastas sem arquivo pai identificado
