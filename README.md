# My Claude Skills

## Sobre

Este repositório contém múltiplas skills que estendem as funcionalidades do Claude Code. Cada skill é um complemento autossuficiente que pode ser invocado dentro do Claude Code para realizar tarefas específicas.

## Skills Disponíveis

### 1. **Obsidian Study Notes Organizer**

Audita e melhora os links internos de um vault Obsidian completo (`.md`) usando o Obsidian CLI (com fallback para Python quando o CLI não estiver disponível).

**O que faz:**

- Executa 5 fases cobrindo o vault inteiro: auditoria intra-pasta, remoção de links quebrados, cross-folder linking, criação do índice central do vault e adição do link do vault em todas as notas
- Aplica skip inteligente: arquivos já corretos são pulados automaticamente
- Opera via Obsidian CLI (preferencial) ou Python direto no sistema de arquivos (fallback)

**Quando usar:**

- Organizar ou melhorar conexões entre notas de todo o vault
- Remover links quebrados ou que apontam para arquivos inexistentes
- Criar ou atualizar o arquivo índice central do vault
- Garantir que todas as notas tenham link do arquivo pai e do vault
- Adicionar relações interdisciplinares entre matérias diferentes

[Como usar →](./.claude/skills/obsidian-study-notes-organizer/README.md)

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
    └── obsidian-study-notes-organizer/
        ├── SKILL.md                   ← instruções para o Claude
        └── README.md                  ← documentação para humanos
```
