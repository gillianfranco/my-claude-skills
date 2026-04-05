---
name: obsidian-study-notes-organizer
description: >
  Audita e melhora links internos entre notas Obsidian (.md) usando o Obsidian CLI. Use sempre que o usuário quiser organizar, auditar ou melhorar as conexões entre notas markdown — incluindo: seção "Related", links entre matérias/pastas diferentes, criação do arquivo índice central do vault, link do arquivo pai do vault em todas as notas, ou remoção de links quebrados/inativos. Ative ao mencionar: "Related", "links entre notas", "arquivo pai", "notas do Obsidian", "auditoria de links", "organizar notas", "relações entre matérias", "vault inteiro", "índice do vault", "links quebrados", "links inativos" — mesmo sem citar a skill explicitamente.
---

# Obsidian Link Auditor

Esta skill executa uma auditoria completa e automática do vault Obsidian, cobrindo desde as relações internas de cada matéria até a estrutura global do vault. **Ao ser chamada, ela sempre executa o fluxo completo de 5 fases**, pulando automaticamente etapas em que os arquivos já estejam corretos.

---

## Modo de operação: CLI ou Python

Antes de qualquer coisa, verifique se o Obsidian CLI está disponível:

```bash
obsidian help
```

- **Se o CLI estiver disponível**: use-o para todas as operações. Os passos abaixo descrevem o fluxo com CLI.
- **Se o CLI não estiver disponível**: use Python com leitura/escrita direta de arquivos `.md`. Leia com `open()`, analise links com regex (`\[\[.*?\]\]`), escreva com `str.replace()`. O fluxo lógico é idêntico — apenas as ferramentas mudam.

---

## Formato de links esperado

```
[[Nome do arquivo sem extensão .md]]
```

A seção Related segue este padrão:
```
### Related:
[[Arquivo Pai da Matéria]]
[[202510232028 - Nota relacionada]]
[[Computação]]
```

---

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

---

## Regra geral: nunca criar arquivos (com uma exceção)

**Não crie arquivos novos**, exceto o arquivo índice central do vault (Fase 4), que pode ser criado se ainda não existir. Fora disso, edite apenas arquivos `.md` já existentes.

---

## Princípio de skip inteligente

**Antes de editar qualquer arquivo, sempre verifique se a edição é necessária.** Se o arquivo já estiver correto para aquela etapa, pule-o sem fazer alterações. Registre quantos foram pulados em cada fase para o relatório final.

Critérios de skip por fase:
- **Fase 1**: a seção Related já tem o link do pai da matéria + links coerentes → pula a nota.
- **Fase 2**: nenhum link na seção Related aponta para arquivo inexistente no vault → pula a nota.
- **Fase 3**: relações cross-folder relevantes já presentes e sem obsoletos → pula a nota.
- **Fase 4**: arquivo índice já existe e lista todas as matérias → skip completo; se incompleto, apenas acrescenta ausentes.
- **Fase 5**: `[[<NomeDoVault>]]` já presente na seção Related → pula a nota.

---

## Passo 0 — Coletar informações do usuário

Antes de começar, colete o que ainda não foi mencionado na conversa:

1. **Nome do vault**: qual o nome do vault Obsidian? (ex: `Computação`)
2. **Caminho da raiz**: qual o caminho completo da pasta raiz do vault no sistema de arquivos?
3. **Links inline**: além da seção Related, devo ajustar links dentro do corpo do texto também? (opcional)

Não pergunte sobre remoção de links irrelevantes — isso é feito automaticamente.

---

## Fase 1 — Auditoria intra-pasta (relações dentro de cada matéria)

Execute para **cada pasta/matéria** do vault.

### 1.1 — Mapear os arquivos da matéria

Liste as notas da pasta:
```bash
obsidian search query="path:<Materia>/Notes/" vault="<NomeDoVault>"
```

Leia o arquivo pai da matéria:
```bash
obsidian read file="<NomeDaMateria>" vault="<NomeDoVault>"
```

Leia cada nota individual:
```bash
obsidian read file="<Nome da nota>" vault="<NomeDoVault>"
```

Monte um mapa de cada arquivo: conceitos principais, seção Related atual, links já presentes.

### 1.2 — Analisar relações intra-pasta

Para cada par de notas da mesma matéria, avalie se existe relação conceitual genuína:
- Um arquivo explica um conceito que o outro pressupõe?
- Um é exemplo ou caso prático do outro?
- Os dois abordam aspectos complementares do mesmo tema?
- Ler um leva naturalmente a querer ler o outro?

**Critérios de qualidade:**
- Um bom link faz o leitor pensar "faz sentido ir lá depois daqui"
- Evite links apenas por palavras-chave compartilhadas
- Prefira relações diretas; ordene por relevância (mais relacionado primeiro)

### 1.3 — Atualizar a seção Related (se necessário)

**Skip:** se a seção Related já contém o link do arquivo pai + links coerentes e sem quebrados → pule este arquivo.

Se precisar atualizar, releia o arquivo antes de editar:
```bash
obsidian read file="<Nome da nota>" vault="<NomeDoVault>"
```

Se a seção `### Related:` **já existir**, substitua:
```bash
obsidian update file="<Nome da nota>" vault="<NomeDoVault>" \
  search="### Related:\n<conteúdo atual>" \
  replace="### Related:\n[[<Pai da Matéria>]]\n[[Nota mais relacionada]]\n[[Segunda mais relacionada]]"
```

Se **não existir**, adicione ao final:
```bash
obsidian append file="<Nome da nota>" vault="<NomeDoVault>" \
  content="\n### Related:\n[[<Pai da Matéria>]]\n[[Nota mais relacionada]]"
```

**Ordem obrigatória na seção Related:**
1. `[[Arquivo pai da matéria]]` — sempre primeiro
2. Links intra-pasta — por relevância
3. Links cross-folder — ao final (adicionados na Fase 3)
4. `[[<NomeDoVault>]]` — sempre último (adicionado na Fase 5)

**Nunca duplique links.**

### 1.4 — Links inline (se solicitado)

Quando o corpo do texto menciona explicitamente um conceito que tem nota própria:
```bash
obsidian update file="<Nome da nota>" vault="<NomeDoVault>" \
  search="<termo mencionado no texto>" \
  replace="[[<Nome da nota>|<termo mencionado no texto>]]"
```

Seja conservador: só adicione quando genuinamente útil para navegação.

---

## Fase 2 — Remoção de links quebrados

Execute para **todas as notas do vault**, em todas as matérias.

### O que é um link quebrado

Um link deve ser removido da seção Related se:
- O arquivo-alvo **não existe** no vault atual (foi deletado ou nunca criado)
- O link aponta para arquivo que **existe em outro vault** — o Obsidian não resolve links cross-vault corretamente; remova-os
- O link está **malformado** (ex: `[[]]`, `[[ ]]`, colchetes incompletos)

**Não remova** links válidos que existem no vault correto, mesmo que sejam de outra pasta/matéria.

### 2.1 — Verificar cada link da seção Related

Para cada nota, extraia todos os links da seção `### Related:` e verifique a existência do arquivo-alvo:

```bash
obsidian read file="<Nome do arquivo linkado>" vault="<NomeDoVault>"
```

Se o comando retornar erro (arquivo não encontrado no vault), o link é quebrado.

Via Python (alternativa):
```python
import os, re

def link_existe_no_vault(nome_link, vault_path):
    """Verifica se o arquivo existe em qualquer subpasta do vault."""
    for root, dirs, files in os.walk(vault_path):
        if f"{nome_link}.md" in files:
            return True
    return False

conteudo = open(nota_path, encoding="utf-8").read()
related_match = re.search(r'### Related:(.*?)(?=\n###|\Z)', conteudo, re.DOTALL)
if related_match:
    links = re.findall(r'\[\[(.+?)(?:\|.+?)?\]\]', related_match.group(1))
    quebrados = [l for l in links if not link_existe_no_vault(l, vault_path)]
```

**Skip:** se nenhum link quebrado for encontrado → não edite o arquivo.

### 2.2 — Remover links quebrados

Se houver links quebrados, substitua a seção Related sem eles:

```bash
obsidian update file="<Nome da nota>" vault="<NomeDoVault>" \
  search="### Related:\n<conteúdo com links quebrados>" \
  replace="### Related:\n<conteúdo apenas com links válidos>"
```

### 2.3 — Registrar links removidos

Mantenha lista interna: nota de origem + link quebrado removido → para o relatório final.

---

## Fase 3 — Cross-folder linking (relações entre matérias diferentes)

### 3.1 — Analisar relações interdisciplinares

Usando o mapa de arquivos construído na Fase 1, avalie pares de notas de **pastas diferentes**:

- A nota A explica um conceito que a nota B pressupõe ou aplica?
- As duas abordam o mesmo fenômeno sob perspectivas diferentes?
- Ler uma leva naturalmente a querer ler a outra, mesmo sendo de matérias distintas?
- Existe sobreposição conceitual relevante (não apenas vocabular)?

**Seja mais criterioso que na Fase 1:** a relação deve ser genuinamente interdisciplinar.

**Skip:** se as relações cross-folder relevantes já estão presentes e não há obsoletos → não edite.

### 3.2 — Verificar links cross-folder já existentes

Antes de adicionar, revise os já presentes:
- O arquivo-alvo existe no vault? (se não → já removido na Fase 2)
- A relação ainda faz sentido?
- O link é redundante?

Remova obsoletos substituindo a seção Related sem eles (mesmo procedimento da Fase 2).

### 3.3 — Adicionar novos links cross-folder

Insira os novos links cross-folder **após** os intra-pasta e **antes** do link do vault (Fase 5):

```bash
obsidian update file="<Nome da nota>" vault="<NomeDoVault>" \
  search="### Related:\n<conteúdo atual>" \
  replace="### Related:\n[[Pai da Matéria]]\n[[Intra-pasta]]\n[[Cross-folder novo]]"
```

**Nunca duplique** — se o link já existir, não adicione novamente.

---

## Fase 4 — Vault Index (arquivo índice central)

### 4.1 — Identificar arquivos pai de cada matéria

Percorra as pastas de primeiro nível do vault e identifique o arquivo pai de cada uma (arquivo `.md` com o mesmo nome da pasta, na raiz da pasta):

```bash
obsidian search query="path:<NomeDaPasta>/<NomeDaPasta>.md" vault="<NomeDoVault>"
```

Via Python:
```python
import os
vault_path = "/caminho/para/vault"
pais = []
for folder in os.listdir(vault_path):
    folder_path = os.path.join(vault_path, folder)
    if os.path.isdir(folder_path) and not folder.startswith('.'):
        pai_path = os.path.join(folder_path, f"{folder}.md")
        if os.path.exists(pai_path):
            pais.append(folder)
```

Ignore pastas de sistema (`.obsidian`, `.trash`, etc.).

### 4.2 — Verificar se o arquivo índice já existe

```bash
obsidian read file="<NomeDoVault>" vault="<NomeDoVault>"
```

- **Se existir**: compare as matérias listadas com as do passo 4.1.
  - Todas já listadas → **skip completo** desta fase.
  - Faltam matérias → apenas acrescente as ausentes; não substitua o arquivo inteiro.
- **Se não existir**: crie o arquivo.

### 4.3 — Criar ou atualizar o arquivo índice

**Formato obrigatório** — lista não ordenada simples, sem títulos nem textos adicionais:
```
- [[Programação]]
- [[Database]]
- [[PHP]]
```

**Criar** (arquivo não existe):
```bash
obsidian create file="<NomeDoVault>" vault="<NomeDoVault>" \
  content="- [[Matéria1]]\n- [[Matéria2]]\n- [[Matéria3]]"
```

Via Python:
```python
index_path = os.path.join(vault_path, f"{nome_vault}.md")
linhas = "\n".join(f"- [[{pai}]]" for pai in sorted(pais))
with open(index_path, "w", encoding="utf-8") as f:
    f.write(linhas + "\n")
```

**Acrescentar matérias ausentes** (arquivo existe mas incompleto):
```bash
obsidian append file="<NomeDoVault>" vault="<NomeDoVault>" \
  content="\n- [[MatériaFaltando]]"
```

### 4.4 — Verificar integridade do arquivo índice

```bash
obsidian read file="<NomeDoVault>" vault="<NomeDoVault>"
```

Confirme: todas as matérias listadas, sem duplicatas, formato `-`, sem títulos extras.

---

## Fase 5 — Vault Parent Link (link do índice central em todas as notas)

### 5.1 — Confirmar existência do arquivo índice central

```bash
obsidian read file="<NomeDoVault>" vault="<NomeDoVault>"
```

Se não existir após a Fase 4, pule esta fase e registre no relatório.

### 5.2 — Verificar e atualizar cada nota do vault

Para cada nota de todas as matérias:

Leia o conteúdo:
```bash
obsidian read file="<Nome da nota>" vault="<NomeDoVault>"
```

**Skip:** se `[[<NomeDoVault>]]` já está presente na seção Related → pule esta nota.

**Se ausente e a seção Related já existir**, adicione o link do vault como **último item**:
```bash
obsidian update file="<Nome da nota>" vault="<NomeDoVault>" \
  search="### Related:\n<conteúdo atual da seção>" \
  replace="### Related:\n<conteúdo atual da seção>\n[[<NomeDoVault>]]"
```

**Se a seção Related não existir** (caso raro após as fases anteriores):
```bash
obsidian append file="<Nome da nota>" vault="<NomeDoVault>" \
  content="\n### Related:\n[[<NomeDoVault>]]"
```

**Nunca duplique** — verifique antes de inserir.

### 5.3 — Confirmar via backlinks

```bash
obsidian backlinks file="<NomeDoVault>" vault="<NomeDoVault>"
```

Todas as notas do vault devem aparecer nos backlinks do arquivo índice.

---

## Passo Final — Relatório ao usuário

Apresente um resumo consolidado cobrindo todas as fases:

**Fase 1 — Auditoria intra-pasta:**
- Notas verificadas / editadas / puladas (já corretas)
- Links intra-pasta adicionados

**Fase 2 — Links quebrados:**
- Total de links quebrados removidos
- Detalhamento: quais notas tiveram links removidos e quais eram (nome do arquivo-alvo inexistente)

**Fase 3 — Cross-folder linking:**
- Notas com novas relações interdisciplinares adicionadas
- Links cross-folder obsoletos removidos
- Pares de matérias com mais conexões identificadas

**Fase 4 — Vault Index:**
- Se o arquivo índice foi criado / atualizado / já estava correto
- Matérias listadas / matérias adicionadas

**Fase 5 — Vault Parent Link:**
- Notas verificadas / com link já presente (puladas) / com link adicionado

**Erros e avisos:**
- Links não adicionados por arquivo-alvo inexistente
- Arquivos ignorados por erro de leitura
- Pastas sem arquivo pai identificado