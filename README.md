# skill-code-review

Skill para o Claude revisar mudanças de código (diff, branch, pull request ou arquivos) e entregar um relatório com achados classificados por severidade, evidência no código (`arquivo:linha`) e correção sugerida.

## O que a skill faz

- Delimita o escopo da revisão (PR, branch, diff local ou arquivos informados) e só lê: não faz commit, push nem comentário sem pedido.
- Entende a intenção da mudança antes de julgar e olha o código ao redor (chamadores, testes, contratos).
- Roda, quando existirem, testes e linters somente leitura, e diz o que não foi executado.
- Revisa seis dimensões: correção, segurança (incluindo LGPD), desempenho, testes, manutenibilidade, compatibilidade e operação.
- Classifica cada achado em Bloqueante, Importante, Sugestão, Dúvida ou Positivo, com nível de confiança.
- Entrega relatório em Markdown com veredito e seção "Não verificado".

## Estrutura

```
skill-code-review/
├── SKILL.md                      # fluxo e princípios da revisão
└── references/
    ├── checklist.md              # perguntas por dimensão
    ├── linguagens.md             # pontos de atenção: Java, PHP, Python, JS/TS, SQL
    └── modelo-relatorio.md       # estrutura do relatório
```

## Como usar

1. Instale a skill: copie esta pasta para o diretório de skills do seu ambiente (por exemplo `.claude/skills/code-review/` em um projeto do Claude Code) ou empacote como `.skill` e adicione ao Claude.
2. Peça em linguagem natural, por exemplo:
   - "Revise o PR https://github.com/org/repo/pull/123"
   - "Faça code review do que mudou na minha branch em relação à main"
   - "Isso está seguro para merge?" (com o diff colado)

## Limites

- Não substitui revisão humana nem ferramentas de análise estática, de dependências ou de segredos. Complementa.
- A qualidade do achado depende do contexto acessível: sem os chamadores, os testes ou a intenção do PR, a skill registra a dúvida em vez de afirmar.
- A skill ainda não passou por avaliação sistemática com casos de teste. Ajuste os arquivos em `references/` ao padrão do seu time.

## Autoria

Fernando Gonçalves de Oliveira · [LinkedIn](https://www.linkedin.com/in/fernandoinfinite/)
