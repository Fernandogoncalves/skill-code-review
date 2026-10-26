---
name: code-review
description: Revisa mudanças de código (diff, branch, pull request, arquivos) e entrega um relatório com achados classificados por severidade, evidência no código e correção sugerida. Use sempre que o usuário pedir para revisar código, PR, merge request, diff ou commit, perguntar "isso está seguro/pronto para merge?", pedir code review, security review ou análise de qualidade de uma mudança, mesmo que não use a expressão "code review". Cobre correção, segurança, desempenho, testes, manutenibilidade e compatibilidade. Use também em inglês (review this PR, check this diff before I merge).
---

# Code review

Revisão de código orientada a evidência. O objetivo é ajudar quem vai fazer o merge a decidir com segurança, não produzir uma lista longa de opiniões. Um review bom é curto nos pontos que importam e honesto sobre o que não foi verificado.

## 1. Delimitar o que revisar

Descubra o escopo antes de ler qualquer coisa, na ordem abaixo, e use a primeira fonte que existir:

1. O que o usuário indicou: URL de PR, hash de commit, branch, arquivos ou trecho colado.
2. Mudanças locais: `git diff` (não commitado), `git diff --staged` e `git diff <base>...HEAD` para uma branch. Descubra a base com `git merge-base` ou pergunte se houver mais de uma candidata.
3. Para PR no GitHub, use `gh pr view` e `gh pr diff` se o `gh` estiver disponível. Sem acesso, peça o diff.

Se o diff for grande (mais de ~800 linhas alteradas ou mais de ~25 arquivos), diga isso no início e proponha dividir a revisão por área em vez de revisar tudo superficialmente. Se o escopo continuar ambíguo depois dessas tentativas, pergunte; revisar o código errado desperdiça mais tempo do que uma pergunta.

Esta skill só lê. Não faça commit, push, comentário em PR nem edição de arquivo sem o usuário pedir. Correções entram no relatório como sugestão (trecho de código ou patch).

## 2. Entender a intenção

Leia descrição do PR, mensagens de commit e issue ligada. Um achado só é válido em relação ao que a mudança pretende fazer. Se a intenção não está clara, registre isso como dúvida no relatório em vez de supor.

Olhe também o entorno: o código alterado raramente é lido sozinho. Abra os chamadores, os testes existentes e as definições dos tipos ou funções tocadas. Muitos falsos positivos de review vêm de julgar um trecho sem ver o contrato ao redor.

## 3. Verificar o que dá para verificar

Se o projeto tiver testes, linter ou type-checker fáceis de rodar (veja `package.json`, `Makefile`, `pom.xml`, `composer.json`, `pyproject.toml`, workflow de CI), rode os que são somente leitura e anote o resultado. Se não rodar, diga que não rodou. Nunca afirme "os testes passam" sem ter visto a execução.

## 4. Revisar por dimensões

Percorra as dimensões abaixo. O detalhamento de cada uma, com perguntas objetivas, está em `references/checklist.md`; leia esse arquivo na primeira revisão da sessão ou quando a mudança tocar segurança, banco de dados ou concorrência. Pontos específicos por linguagem estão em `references/linguagens.md` (Java, PHP, Python, JavaScript/TypeScript, SQL).

- **Correção**: a mudança faz o que promete? Casos de borda, nulos, erros, tempo e fuso, arredondamento, concorrência, idempotência.
- **Segurança**: entrada não confiável, injeção, autenticação e autorização, segredos, exposição de dados pessoais (LGPD).
- **Desempenho**: consultas N+1, laços com I/O, consultas sem limite, complexidade, bloqueio de thread.
- **Testes**: a mudança de comportamento está coberta? Os testes falhariam se o código estivesse errado?
- **Manutenibilidade**: nomes, tamanho, duplicação, acoplamento, comentários que explicam o porquê.
- **Compatibilidade e operação**: API pública, migrações de banco, flags, rollback, logs e métricas.

## 5. Classificar cada achado

Cada achado precisa de local (`arquivo:linha`), problema, por que importa e correção sugerida. Sem evidência no código, não vira achado; vira dúvida.

| Severidade | Quando usar |
|---|---|
| Bloqueante | Bug que causa dano real, falha de segurança, perda de dados ou quebra de contrato. Não deve ser mesclado assim. |
| Importante | Provável problema em produção ou dívida cara, mas com contorno. Deve ser corrigido ou ter justificativa. |
| Sugestão | Melhoria de legibilidade, estrutura ou desempenho sem risco imediato. Fica a critério do autor. |
| Dúvida | Falta contexto para julgar. Formule como pergunta. |
| Positivo | Decisão boa que vale manter. Use com parcimônia e só quando for específico. |

Informe a confiança quando não for alta ("confiança média: depende de como `X` é chamado em outro módulo"). Distinga fato (o código faz isso), inferência (provavelmente causa isso) e opinião (eu faria diferente).

Não aponte o que o linter ou formatador do projeto já resolve, nem preferências de estilo sem consequência. Mais de ~5 sugestões menores diluem os achados que importam; agrupe-as ou escolha as melhores.

## 6. Entregar o relatório

Use a estrutura em `references/modelo-relatorio.md`. Em resumo:

1. **Resumo** em 3 a 5 linhas: o que a mudança faz, veredito (Aprovar / Aprovar com ressalvas / Solicitar mudanças) e os pontos que decidem o veredito.
2. **Achados** ordenados por severidade, cada um com local, problema, impacto e correção.
3. **Não verificado**: o que ficou de fora e por quê (testes não executados, código chamador não acessível, arquivos ignorados).
4. **Perguntas ao autor**, se houver.

Responda no idioma em que o usuário escreveu. Mantenha o relatório em Markdown, pronto para colar em PR ou ticket. Se o usuário pedir, ofereça o relatório já dividido em comentários por arquivo e linha.

## Princípios que sustentam o método

- Evidência antes de opinião: um achado sem trecho de código que o sustente é palpite.
- Escopo do diff: critique o que mudou. Problemas antigos fora do diff entram numa seção separada e curta, só se forem relevantes para a mudança.
- Proporção: uma mudança pequena merece um review pequeno. Um relatório mais longo que o diff é sinal de que faltou priorização.
- Honestidade sobre limites: dizer o que não foi verificado vale mais do que uma aprovação confiante demais.
- Código gerado por IA, inclusive o seu, precisa do mesmo rigor e de revisão humana antes de ser tratado como pronto.
