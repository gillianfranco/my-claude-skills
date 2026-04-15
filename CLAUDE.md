# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## O que é este repositório

Coleção de skills personalizadas para o Claude Code. Cada skill fica em `.claude/skills/<nome-da-skill>/` e é carregada automaticamente pelo Claude Code quando o diretório está no caminho de busca de skills.

## Estrutura de uma skill

Cada skill tem exatamente dois arquivos:

- **`SKILL.md`** — instruções para o Claude (frontmatter YAML + prompt de sistema). É o arquivo que o Claude Code executa.
- **`README.md`** — documentação para humanos. Explica o que a skill faz, como invocá-la e o que esperar de saída.

### Frontmatter obrigatório no `SKILL.md`

```yaml
---
name: nome-da-skill
description: >
  Descrição detalhada de quando acionar a skill. Inclui palavras-chave
  e gatilhos de invocação que o Claude Code usa para detectar automaticamente.
---
```

## Adicionando uma nova skill

Ao adicionar uma nova skill ao repositório, **sempre** atualize também:

1. **`.claude/skills/<nova-skill>/README.md`** — siga o padrão das skills existentes:
   - Título + descrição curta
   - `## O que faz` — lista com as etapas/fases do fluxo
   - Seção de modo de operação ou comportamento especial (se houver)
   - `## Como usar` — exemplos de invocação em linguagem natural
   - `## O que a skill pergunta` — inputs que a skill coleta do usuário
   - `## Formato de saída` — estrutura do que a skill retorna

2. **`README.md` (raiz)** — adicione a nova skill em "Skills Disponíveis":
   - Número sequencial, nome em negrito
   - Descrição em uma linha
   - Bloco "O que faz" com 3–4 bullets
   - Bloco "Quando usar" com casos de uso
   - Link `[Como usar →](./.claude/skills/<nome>/README.md)`
   - Atualize também a árvore em "Estrutura do Projeto"

## Skills disponíveis

| Skill | Descrição curta |
|---|---|
| `obsidian-study-notes-organizer` | Audita e corrige links internos de um vault Obsidian inteiro em 5 fases |
| `idosoeidosa-article-creator` | Gera artigos WordPress Gutenberg otimizados para SEO para idosoeidosa.com.br |

## Instalação

As skills não são publicadas em marketplace. Para usá-las em outros projetos:

```bash
git clone https://github.com/gillianfranco/my-claude-skills ~/my-claude-skills
ln -s ~/my-claude-skills/.claude/skills/* ~/.claude/skills/
```

Para atualizar: `cd ~/my-claude-skills && git pull origin main`
