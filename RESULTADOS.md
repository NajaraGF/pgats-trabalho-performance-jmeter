# Resultado da execução local

Execução em 26/11/2026, aproximadamente 20h35 (Brasília), JMeter 5.6.3 e API oficial em 127.0.0.1:3109.

48 requisições, todas aprovadas pelas validações (0 erros): 18 no modelo fechado e 30 no aberto. Foram realizadas 6 sessões no fechado e 10 no aberto, cada uma com login, cadastro e consulta.

| Operação | Amostras | Erros | Média (ms) | Máximo (ms) |
|---|---:|---:|---:|---:|
| Login | 16 | 0 | 40.50 | 150 |
| Cadastrar pet | 16 | 0 | 3.25 | 12 |
| Listar pets | 16 | 0 | 4.88 | 25 |

A tentativa anterior teve falhas de conexão por restrição do ambiente de execução. Após liberar a conexão local, a execução acima terminou sem erros. Não houve alteração dos resultados para ocultar falhas.

Estes resultados verificam o funcionamento do plano com carga pequena. Não demonstram capacidade máxima nem cumprimento de SLA. API e JMeter compartilharam o mesmo computador.

Commit da API utilizado: `c82ba51f921435034b479d22f9deda963ad01ccd`.
