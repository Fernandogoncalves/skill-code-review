# Pontos de atenção por linguagem

Leia apenas a seção da linguagem em revisão. São lembretes dos erros mais comuns em cada ecossistema, não um guia completo; a evidência no código revisado continua sendo o que decide se algo vira achado.

## Java

- `equals` sem `hashCode` (e vice-versa); comparação de `String` ou wrappers com `==`.
- `Optional.get()` sem checagem; retorno de `null` onde o contrato sugere coleção vazia.
- Recursos sem `try-with-resources` (streams, conexões, `ResultSet`).
- Exceção capturada e ignorada, ou `catch (Exception)` amplo que esconde falha de programação.
- Concorrência: coleções não thread-safe compartilhadas, `SimpleDateFormat` estático, `synchronized` sobre objeto errado.
- JPA/Hibernate: N+1 por relação `LAZY` acessada em laço, `EAGER` em relação grande, transação em método chamado por `this` (proxy não é aplicado), `@Transactional` em método privado.
- Dinheiro em `double` em vez de `BigDecimal`; `BigDecimal` criado a partir de `double`.
- SQL concatenado em `Statement` em vez de `PreparedStatement` ou parâmetros nomeados.

## PHP

- Entrada de `$_GET`, `$_POST`, `$_FILES` e cabeçalhos usada sem validação; SQL montado por concatenação em vez de consultas preparadas (PDO).
- Comparação frouxa (`==`) onde se espera comparação estrita (`===`), principalmente com hashes, tokens e `in_array` sem o terceiro argumento.
- `include`/`require` com caminho vindo de entrada; `eval`, `unserialize` de dados não confiáveis, `extract` sobre entrada.
- Saída sem `htmlspecialchars` (XSS); upload de arquivo validado só pela extensão ou pelo tipo enviado pelo cliente.
- Erros suprimidos com `@`; `error_reporting` e `display_errors` ativos em produção.
- Senhas com `md5` ou `sha1` em vez de `password_hash` e `password_verify`.
- Sessão: regeneração do identificador após login, flags do cookie.

## Python

- Argumento padrão mutável (`def f(x=[])`); variável de laço capturada em closure tardia.
- `except:` ou `except Exception:` que engole erro; `assert` usado para validar entrada (removido com `-O`).
- `subprocess` com `shell=True` e entrada externa; `pickle`, `yaml.load` sem `SafeLoader`, `eval`/`exec`.
- SQL por f-string em vez de parâmetros do driver ou do ORM.
- Arquivos e conexões sem `with`; `datetime` ingênuo (sem fuso) misturado com datetime com fuso.
- Django/SQLAlchemy: N+1 (falta `select_related`/`prefetch_related`, `joinedload`), consultas em laço, migrações que bloqueiam tabela grande.
- Tipagem: hints que não batem com o retorno real quando o projeto usa checagem estática.

## JavaScript e TypeScript

- Promise sem `await` ou sem tratamento de rejeição; `async` dentro de `forEach` esperando sequência.
- Comparação com `==`; coerção em `if (valor)` quando `0`, `""` ou `false` são valores legítimos.
- Mutação de estado (React/Redux) ou de props; dependências erradas ou ausentes em `useEffect`; `key` instável em listas.
- `any`, `as` e `!` que apagam a checagem de tipos exatamente onde o risco está; `JSON.parse` sem validação de formato.
- XSS: `innerHTML`, `dangerouslySetInnerHTML`, interpolação em URL ou atributo sem tratamento.
- Node: `child_process` com entrada externa, caminhos vindos do usuário, `require` dinâmico, vazamento de segredos para o bundle do cliente.
- Dependências: versão não fixada, pacote novo com poucos mantenedores ou nome parecido com um popular (typosquatting).

## SQL

- Concatenação de texto em vez de parâmetros (injeção); `SELECT *` em código de aplicação que depende da ordem das colunas.
- `UPDATE` ou `DELETE` sem `WHERE`, ou com `WHERE` mais amplo do que o pretendido; faltou transação em operação de várias etapas.
- Função sobre coluna indexada no filtro, `LIKE '%texto'`, `OR` que derruba o uso de índice, junção sem condição (produto cartesiano).
- `NOT IN` com possíveis `NULL`; `COUNT(coluna)` versus `COUNT(*)`; comparação com `NULL` usando `=`.
- Migração: `ALTER TABLE` que reescreve ou bloqueia tabela grande, coluna `NOT NULL` sem valor padrão, remoção de coluna ainda lida pela versão anterior do código, índice criado sem a opção de não bloquear quando o banco oferece.
- Isolamento e concorrência: leitura-modificação-escrita sem bloqueio ou controle de versão; ordem de bloqueio inconsistente entre transações (deadlock).
- Dados pessoais em colunas, logs de consulta e cópias de teste sem anonimização.
