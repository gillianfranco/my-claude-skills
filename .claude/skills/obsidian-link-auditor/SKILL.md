---
name: obsidian-link-auditor
description: >
  Audita e melhora os links internos entre notas Obsidian (.md) em uma pasta usando o Obsidian CLI. Use esta skill sempre que o usuário quiser: organizar ou melhorar as conexões entre notas markdown, auditar a seção "Related" de arquivos Obsidian, garantir que todas as notas contenham o link do arquivo pai (índice), revisar se os links entre notas estão corretos e completos, adicionar links que estão faltando na seção Related, remover links irrelevantes de notas, ou melhorar a interconexão de qualquer pasta de notas markdown. Ative sempre que o usuário mencionar "Related", "links entre notas", "arquivo pai", "notas do Obsidian", "auditoria de links", "organizar notas", "pasta de markdown" ou variações disso — mesmo que o usuário não chame explicitamente de "skill".
---

# Obsidian Link Auditor

Esta skill audita e melhora os links internos entre notas Obsidian em uma pasta, garantindo que a seção `### Related:` de cada arquivo contenha links relevantes e que o arquivo pai (índice) esteja linkado em todos os arquivos.

## Modo de operação: CLI ou Python

Antes de qualquer coisa, verifique se o Obsidian CLI está disponível:

```bash
obsidian help
```

- **Se o CLI estiver disponível**: use-o para todas as operações (leitura, edição, busca de backlinks). Os passos abaixo descrevem o fluxo com CLI.
- **Se o CLI não estiver disponível** (comando não encontrado, Obsidian fechado, etc.): use Python com leitura/escrita direta de arquivos `.md` pelo sistema de arquivos. Leia os arquivos com `open()`, analise os links com regex (`\[\[.*?\]\]`), e escreva as alterações de volta com `str.replace()` ou manipulação de string. O fluxo lógico (passos 1–6) é idêntico — apenas as ferramentas mudam.

## Formato de links esperado

As notas usam o formato wiki-link do Obsidian:

```
[[Nome do arquivo sem extensão .md]]
```

A seção Related segue este padrão:

```
### Related:
[[202510232028 - Principais protocolos de rede]]
[[202510242044 - O que é DNS]]
```

## Estrutura de pastas esperada

```
Culinária/                   ← pasta raiz do conteúdo
├── Culinária.md             ← arquivo pai (mesmo nome da pasta)
└── Notes/                   ← pasta com as notas individuais
    ├── 202501011000 - Nota A.md
    ├── 202501011010 - Nota B.md
    └── ...
```

A skill é invocada na pasta raiz do conteúdo (ex: `Culinária/`). O arquivo pai está nessa raiz, e as notas estão dentro de `Notes/`.

## Regra absoluta: nunca criar arquivos

**Não crie arquivos novos em nenhuma circunstância.** Sua função é exclusivamente editar os arquivos `.md` já existentes dentro da pasta `Notes/`. Se o arquivo pai não existir na pasta raiz, apenas ignore a etapa de adicionar o link pai.

---

## Passo 1 — Coletar informações do usuário

Antes de começar qualquer trabalho, use `AskUserQuestion` para coletar o que ainda não foi mencionado na conversa:

1. **Vault e pasta de notas**: qual o nome do vault Obsidian e o caminho da pasta raiz do conteúdo (ex: `Culinária/`)?
2. **Links irrelevantes**: devo remover links que parecem pouco relevantes, ou apenas adicionar os que faltam?
3. **Links inline**: além da seção Related, devo também ajustar links dentro do corpo do texto?

---

## Passo 2 — Ler todos os arquivos via CLI

Use o Obsidian CLI para listar e ler os arquivos. **Prefira sempre o CLI** ao invés de ferramentas genéricas de arquivo — o CLI resolve caminhos como wikilinks e garante que você lê exatamente o que o Obsidian enxerga.

### 2.1 — Listar arquivos na pasta Notes

```bash
obsidian search query="path:Notes/" vault="<NomeDoVault>"
```

> Use `obsidian help` para confirmar o comando exato de listagem disponível na sua versão do CLI.

### 2.2 — Ler cada arquivo

```bash
obsidian read file="<Nome do arquivo sem extensão>" vault="<NomeDoVault>"
```

Exemplo:

```bash
obsidian read file="202501011000 - Nota A" vault="Culinária"
```

### 2.3 — Ler o arquivo pai (se existir)

```bash
obsidian read file="<NomeDaPastaRaiz>" vault="<NomeDoVault>"
```

### 2.4 — Verificar backlinks existentes

Para entender as conexões já existentes, consulte os backlinks de cada nota:

```bash
obsidian backlinks file="<Nome do arquivo>" vault="<NomeDoVault>"
```

Monte um mapa mental de cada arquivo: título, conceitos principais, links já existentes na seção Related, e backlinks recebidos.

---

## Passo 3 — Analisar relações entre os arquivos

Para cada par de arquivos, avalie se existe uma relação conceitual genuína:

- Um arquivo explica um conceito que o outro pressupõe?
- Um arquivo é um exemplo ou caso prático do conceito do outro?
- Os dois abordam aspectos complementares do mesmo tema?
- Ler um leva naturalmente a querer ler o outro?

Organize os arquivos em clusters temáticos e identifique quais servem como "hubs" (mais conexões) e quais são mais específicos.

**Critérios de qualidade para links:**

- Um bom link no Related faz o leitor pensar "faz sentido ir lá depois daqui"
- Evite linkar arquivos apenas porque compartilham uma palavra-chave
- Prefira relações diretas a relações indiretas
- Ordene por relevância (mais relacionado primeiro)

---

## Passo 4 — Atualizar cada arquivo via CLI

**Nunca edite arquivos diretamente pelo sistema de arquivos** — sempre use o CLI para garantir que o Obsidian reconheça as mudanças em tempo real.

### 4.1 — Releia o arquivo antes de editar

```bash
obsidian read file="<Nome do arquivo>" vault="<NomeDoVault>"
```

### 4.2 — Atualizar a seção Related

Se a seção `### Related:` **já existir**, substitua seu conteúdo:

```bash
obsidian update file="<Nome do arquivo>" vault="<NomeDoVault>" \
  search="### Related:\n<conteúdo atual>" \
  replace="### Related:\n[[Arquivo Pai]]\n[[Arquivo mais relacionado]]\n[[Segundo mais relacionado]]"
```

> Use `\n` para quebras de linha. Consulte `obsidian help update` para confirmar a sintaxe exata.

Se a seção **não existir**, adicione-a ao final:

```bash
obsidian append file="<Nome do arquivo>" vault="<NomeDoVault>" \
  content="\n### Related:\n[[Arquivo Pai]]\n[[Arquivo mais relacionado]]"
```

### 4.3 — Regras para o conteúdo da seção Related

- **Link do arquivo pai**: sempre como **primeiro item**, se informado e se o arquivo pai existir. Nunca duplique.
- **Se "remover irrelevantes"**: substitua a seção inteira com apenas os links relevantes.
- **Se "só adicionar"**: mantenha os links existentes e acrescente os que faltam.
- **Ordem**: mais relacionado primeiro.

**Formato correto após edição:**

```
### Related:
[[Arquivo Pai]]
[[Arquivo mais relacionado]]
[[Segundo mais relacionado]]
```

### 4.4 — Links inline (se solicitado)

Quando o corpo do texto menciona explicitamente um conceito que tem nota própria:

```bash
obsidian update file="<Nome do arquivo>" vault="<NomeDoVault>" \
  search="<termo mencionado no texto>" \
  replace="[[<Nome da nota>|<termo mencionado no texto>]]"
```

Seja conservador: só adicione inline links quando ajudam genuinamente a navegação.

---

## Passo 5 — Verificação final via CLI

### 5.1 — Confirmar que os arquivos linkados existem

Para cada link adicionado, verifique que o arquivo-alvo existe:

```bash
obsidian read file="<Nome do arquivo linkado>" vault="<NomeDoVault>"
```

Se o arquivo não existir, remova o link correspondente.

### 5.2 — Confirmar link do arquivo pai via backlinks

```bash
obsidian backlinks file="<Arquivo Pai>" vault="<NomeDoVault>"
```

Os arquivos da pasta `Notes/` devem aparecer nos backlinks. Se algum estiver faltando, adicione o link.

### 5.3 — Verificar integridade dos arquivos editados

Releia uma amostra dos arquivos para confirmar que a estrutura (seções Sources, Keywords, etc.) foi preservada:

```bash
obsidian read file="<Nome do arquivo>" vault="<NomeDoVault>"
```

---

## Passo 6 — Relatório final ao usuário

Informe ao usuário:

- Quantos arquivos foram processados
- Quantos links foram adicionados / removidos
- Se o link do arquivo pai foi adicionado e em quantos arquivos
- Quais são os clusters temáticos identificados (resumidamente)
- Se algum link foi rejeitado por apontar para arquivo inexistente
