# My Claude Skills

## Sobre

Este repositório contém múltiplas skills que estendem as funcionalidades do Claude Code. Cada skill é um complemento autossuficiente que pode ser invocado dentro do Claude Code para realizar tarefas específicas.

## Skills Disponíveis

### 1. **Obsidian Link Auditor**

Audita e melhora os links internos entre notas Obsidian (`.md`) em uma pasta usando o Obsidian CLI (com fallback para Python quando o CLI não estiver disponível).

**O que faz:**

- Verifica e organiza a seção `### Related:` em cada nota
- Adiciona links para o arquivo pai (índice) automaticamente
- Identifica relações conceituais entre arquivos
- Consulta backlinks via Obsidian CLI para mapear conexões existentes
- Remove links irrelevantes (opcional)
- Adiciona links inline contextuais (opcional)

**Quando usar:**

- Organizar ou melhorar conexões entre notas markdown
- Auditar a seção "Related" de arquivos Obsidian
- Garantir que todas as notas contenham link do arquivo pai
- Revisar completude de links entre notas

[Como usar →](./.claude/skills/obsidian-link-auditor/README.md)

## Instalação em Outros Projetos

Como o plugin não está publicado em um marketplace, a forma de usá-lo em outros projetos é clonar este repositório e criar um link simbólico para o diretório global de skills do Claude Code.

**Setup:**

```bash
# 1. Clone o repositório
git clone https://github.com/gillianfranco/my-claude-skills ~/my-claude-skills

# 2. Crie um symlink no diretório global de skills do Claude Code
ln -s ~/my-claude-skills/.claude/skills/* ~/.claude/skills/
```

As skills estarão disponíveis automaticamente em todos os projetos.

**Para atualizar as skills no futuro:**

```bash
cd ~/my-claude-skills && git pull origin main
```

## Estrutura do Projeto

```
my-claude-skills/
├── README.md                          ← Você está aqui
├── .claude-plugin/
│   └── plugin.json                    ← Manifesto do plugin
└── .claude/skills/
    └── obsidian-link-auditor/
        ├── SKILL.md                   ← instruções para o Claude
        └── README.md                  ← documentação para humanos
```
