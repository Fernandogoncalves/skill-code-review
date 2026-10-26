# Checklist por dimensão

Perguntas para orientar a leitura. Não é lista de verificação a cumprir item a item: use para encontrar o que está errado na mudança em questão e ignore o que não se aplica.

## Sumário

1. Correção
2. Segurança
3. Desempenho
4. Testes
5. Manutenibilidade
6. Compatibilidade e operação

## 1. Correção

- A mudança implementa o que a descrição do PR promete? Há comportamento alterado que não foi mencionado?
- O que acontece com entrada vazia, nula, muito grande, duplicada, fora de ordem ou com caracteres especiais?
- Condições de limite: `<` versus `<=`, índice zero, lista vazia, primeiro e último elemento.
- Erros: exceções são engolidas? O erro chega a quem precisa tratá-lo, com contexto? Recursos são liberados em caminho de falha (arquivos, conexões, locks)?
- Tempo: fuso horário, horário de verão, comparação de datas com e sem hora, dependência do relógio do servidor.
- Números: arredondamento, overflow, tipo de ponto flutuante para dinheiro, divisão por zero.
- Concorrência: estado compartilhado sem sincronização, check-then-act, ordem de operações assíncronas, transações que cobrem menos do que deveriam.
- Idempotência: reexecutar a operação (retry, reprocessamento de fila) produz efeito duplicado?

## 2. Segurança

- Toda entrada externa é tratada como não confiável (parâmetros, cabeçalhos, arquivos, corpo de requisição, dados de outro serviço)?
- Injeção: SQL concatenado em vez de parâmetros, comando de shell com entrada do usuário, templates, LDAP, XPath, expressões.
- Autenticação e autorização: a checagem acontece no servidor e em toda rota nova? O usuário só acessa os próprios recursos (IDOR)? Há escalada de privilégio por parâmetro?
- Segredos: chaves, tokens e senhas em código, em log, em mensagem de erro ou em arquivo versionado.
- Dados pessoais (LGPD): o dado coletado é necessário? Aparece em log, URL ou cache? Há base legal e retenção definidas? Dados sensíveis estão protegidos em trânsito e em repouso?
- Requisições feitas pelo servidor a URLs informadas pelo usuário (SSRF), redirecionamentos abertos, upload de arquivo sem validação de tipo e tamanho, travessia de caminho (`../`).
- Desserialização de dados não confiáveis, uso de funções de execução dinâmica, dependências novas com versão sem fixar ou com vulnerabilidade conhecida.
- Criptografia: algoritmo e modo adequados, nada caseiro, números aleatórios de fonte criptográfica quando o contexto exigir.
- Saída: escape adequado ao contexto (HTML, atributo, JavaScript, URL) para evitar XSS; cabeçalhos de segurança em respostas novas.

Para cobertura de categorias de risco web, use o OWASP Top 10 como referência de vocabulário, citando a categoria quando ajudar o autor a pesquisar.

## 3. Desempenho

- Consulta dentro de laço (N+1), carregamento ansioso de relações que não são usadas, falta de paginação ou de limite.
- Filtros e junções sem índice de apoio; funções aplicadas sobre coluna indexada que impedem o uso do índice.
- Complexidade: laços aninhados sobre coleções que crescem, ordenação ou busca linear repetida.
- I/O bloqueante em caminho quente, chamadas de rede sequenciais que poderiam ser em lote, ausência de timeout.
- Memória: carregar arquivo ou resultado inteiro quando streaming resolve, cache sem expiração ou sem limite.
- Só aponte desempenho quando houver caminho plausível de volume. Sem medição, marque como hipótese e sugira como medir.

## 4. Testes

- O comportamento novo ou alterado tem teste? Bugs corrigidos têm teste que reproduz o bug?
- O teste falharia se o código estivesse errado? Cuidado com asserções fracas (só verifica que não lançou exceção), mocks que reproduzem a implementação e testes que dependem de ordem, relógio ou rede.
- Casos de borda e de erro estão cobertos, não só o caminho feliz?
- Dados de teste não contêm dado pessoal real.

## 5. Manutenibilidade

- Nomes dizem a intenção? Funções fazem uma coisa? Parâmetros booleanos que mudam o comportamento são sinal de função dividida em duas.
- Duplicação que vai divergir; abstração criada cedo demais para um único uso.
- Acoplamento: a mudança força alterar muitos módulos? Dependências circulares novas?
- Comentários explicam o porquê, não o quê. Código morto, TODO sem dono e flags antigas removidos ou registrados.
- Consistência com as convenções do projeto, desde que sejam convenções reais (veja o restante da base), não preferência pessoal.

## 6. Compatibilidade e operação

- Contrato público (API, evento, esquema, arquivo): campo removido, tipo alterado ou semântica diferente quebra consumidores?
- Migração de banco: é reversível? Bloqueia tabela grande? Funciona com a versão antiga e a nova do código rodando ao mesmo tempo durante o deploy?
- Rollout: existe flag, ordem de deploy ou passo manual que precisa ser documentado? Qual é o plano de rollback?
- Observabilidade: erros novos são logados com contexto suficiente e sem dado sensível? Métricas ou alertas precisam mudar?
- Configuração nova tem valor padrão seguro e está documentada.
