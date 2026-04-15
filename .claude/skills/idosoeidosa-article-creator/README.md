# Idoso e Idosa Article Creator

Gera artigos de blog completos, otimizados para SEO e ranqueamento no Google, para o site **idosoeidosa.com.br**.

## O que faz

Executa um fluxo de **4 passos** para produzir artigos prontos para publicação no WordPress Gutenberg:

- **Passo 1 — Pesquisa**: realiza ao menos 2 buscas na web antes de escrever, coletando dados de fontes confiáveis (OMS, Ministério da Saúde, IBGE, CFM, SBGG) — nunca inventa dados ou fontes
- **Passo 2 — Estrutura**: organiza o artigo com introdução (resposta direta + gancho + lead), mínimo de 5 seções H3 numeradas, quebra de crença, cluster temático, conclusão e referências ABNT
- **Passo 3 — Escrita**: aplica regras editoriais de tom, extensão (800–1.800 palavras), parágrafos curtos para mobile, uso correto de negrito e listas, e palavra-chave inserida de forma natural
- **Passo 4 — Saída Gutenberg**: entrega o artigo inteiramente em blocos HTML do WordPress (Gutenberg), com zero Markdown, pronto para colar no editor de código

## Público-alvo do conteúdo

- Pessoas com 60+ anos
- Familiares de idosos
- Cuidadores formais e informais

## Como usar

Dentro do Claude Code, mencione a tarefa em linguagem natural. A skill é ativada automaticamente por palavras-chave como "escreva um artigo", "crie um post", "gere conteúdo para o blog", temas de saúde/rotina/direitos/cuidados com idosos, ou pedidos de conteúdo otimizado para SEO no contexto da terceira idade.

**Exemplos de invocação:**

```
"Escreva um artigo sobre hidratação em idosos"
"Crie um post para o blog sobre sinais de depressão na terceira idade"
"Gere conteúdo SEO sobre medicamentos e idosos para o idosoeidosa.com.br"
"Quero um artigo sobre alimentação saudável para idosos"
"Tema: solidão na terceira idade. Palavra-chave: solidão em idosos"
```

## O que a skill pergunta

Ao ser invocada, a skill coleta o que ainda não foi informado na conversa:

1. **Tema do artigo** — ex: `hidratação em idosos`
2. **Palavra-chave principal** — opcional; se omitida, a skill infere com base no contexto do blog
3. **Público principal** — `idoso`, `cuidador`, `familiar` ou `geral`; se omitido, a skill infere pelo tema

## Formato de saída

O artigo é entregue em uma caixa de código (` ``` `) para facilitar a cópia, contendo exclusivamente blocos HTML Gutenberg na seguinte ordem:

- **Introdução**: parágrafo de resposta direta + gancho + lead
- **Placeholder de imagem única**: posicionado logo após a introdução e antes das seções numeradas
- **Mínimo 5 seções numeradas**: cada uma com separator, H3, parágrafo em negrito, desenvolvimento, lista (quando aplicável) e blockquote de dica
- **Conclusão**: H3 "Conclusão" com resumo e frase forte de encerramento
- **Referências**: H3 "Referências" com fontes no formato ABNT
