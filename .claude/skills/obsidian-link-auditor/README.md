# Obsidian Link Auditor

Audita e melhora os links internos entre notas Obsidian (`.md`) em uma pasta usando o Obsidian CLI (com fallback para Python quando o CLI não estiver disponível).

## O que faz

- Verifica e organiza a seção `### Related:` em cada nota
- Adiciona links para o arquivo pai (índice) automaticamente
- Identifica relações conceituais entre arquivos
- Consulta backlinks via Obsidian CLI para mapear conexões existentes
- Remove links irrelevantes (opcional)
- Adiciona links inline contextuais (opcional)

## Modo de operação

A skill opera em dois modos:

- **Obsidian CLI** (preferencial): usa comandos como `obsidian read`, `obsidian update`, `obsidian backlinks` e `obsidian append` para ler, editar e consultar backlinks diretamente pelo CLI. Isso garante que o Obsidian reconheça as mudanças em tempo real.
- **Python** (fallback): quando o CLI não está disponível (comando não encontrado, Obsidian fechado, etc.), a skill usa leitura/escrita direta de arquivos `.md` pelo sistema de arquivos com Python.

A detecção é automática — a skill verifica a disponibilidade do CLI antes de começar.

## Estrutura de pastas esperada

```
Culinária/                   ← pasta raiz do conteúdo
├── Culinária.md             ← arquivo pai (mesmo nome da pasta)
└── Notes/                   ← pasta com as notas individuais
    ├── 202501011000 - Nota A.md
    ├── 202501011010 - Nota B.md
    └── ...
```

## Como usar

Dentro do Claude Code, mencione a tarefa em linguagem natural. A skill é ativada automaticamente por palavras-chave como "Related", "links entre notas", "arquivo pai", "notas do Obsidian", "auditoria de links", "organizar notas" ou "pasta de markdown".

**Exemplos de invocação:**

```
"Auditoria de links nas minhas notas de Culinária"
"Organize os links entre notas na pasta ~/Vault/Redes"
"Adicione links faltando na seção Related"
"Garanta que todas as notas tenham o link do arquivo pai"
```

## O que a skill pergunta

Ao ser invocada, a skill coleta as seguintes informações antes de começar:

1. **Vault e pasta raiz** — nome do vault Obsidian e caminho da pasta que contém o arquivo pai e a subpasta `Notes/`
2. **Remover links irrelevantes?** — apenas adicionar os que faltam, ou também remover os que não fazem sentido
3. **Links inline?** — adicionar wiki-links dentro do corpo do texto quando o contexto permitir

Se alguma dessas informações já foi mencionada na conversa, a skill usa o que foi dito e não pergunta novamente.

## Formato de saída

Ao final, a skill informa:

- Quantos arquivos foram processados
- Quantos links foram adicionados / removidos
- Se o link do arquivo pai foi adicionado e em quantos arquivos
- Quais clusters temáticos foram identificados
- Se algum link foi rejeitado por apontar para arquivo inexistente
