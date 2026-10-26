# Modelo de relatório

Use esta estrutura. Remova as seções que não tiverem conteúdo (por exemplo, "Perguntas ao autor" quando não houver dúvidas) em vez de preenchê-las por formalidade. O relatório deve caber em uma tela ou duas; se passar disso, falta priorização.

````markdown
# Code review: <título da mudança ou PR>

**Escopo**: <PR/branch/commit/arquivos revisados> · **Base**: <branch ou commit> · **Tamanho**: <N arquivos, +X −Y linhas>

## Resumo

<3 a 5 linhas: o que a mudança faz, veredito e os pontos que decidem o veredito.>

**Veredito**: Aprovar | Aprovar com ressalvas | Solicitar mudanças

| Severidade | Qtde |
|---|---|
| Bloqueante | 0 |
| Importante | 0 |
| Sugestão | 0 |
| Dúvida | 0 |

## Achados

### [Bloqueante] <título curto do problema>

- **Local**: `caminho/arquivo.ext:linha`
- **Problema**: <o que o código faz de errado, com o trecho relevante>
- **Impacto**: <o que acontece, em que condição>
- **Correção sugerida**: <descrição e, se couber, trecho de código ou patch>
- **Confiança**: alta | média | baixa (<por quê, se não for alta>)

### [Importante] ...

### [Sugestão] ...

## Não verificado

- <o que ficou de fora e por quê: testes não executados, chamadores não revisados, arquivos gerados ignorados>

## Perguntas ao autor

1. <pergunta objetiva sobre intenção ou contexto que faltou>

## Positivos

- <decisão específica que vale manter>
````

## Comentários por linha (quando o usuário pedir)

Entregue uma lista no formato `arquivo:linha — [severidade] comentário`, um por item, na ordem dos arquivos do diff, pronta para ser transcrita como comentário de revisão. Mantenha cada comentário autocontido: o autor deve entender o problema sem ler o resto do relatório.

## Tom

Fale do código, não da pessoa. Prefira "este laço executa uma consulta por item" a "você esqueceu de otimizar". Quando houver alternativa melhor, mostre-a. Quando for gosto pessoal, diga que é opinião.
