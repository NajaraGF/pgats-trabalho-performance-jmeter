# PGATS — Automação de Testes de Performance

Trabalho extra da disciplina 09, Aula 6, com Apache JMeter 5.6.3.

## API e referências

API oficial indicada pelo professor no chat da Aula 5: https://github.com/tiagotaq/api-de-pets. O README, `rest/swagger.js` e os controllers definem o contrato. Swagger disponível em `/api-docs` quando a API está em execução. Os materiais privados da disciplina não integram este repositório.

## Plano `pets.jmx`

Os grupos executam sequencialmente, evitando sobreposição de carga:

- **Fechado:** Thread Group padrão, 2 usuários, ramp-up de 2 segundos e 3 iterações por usuário. Cada usuário aguarda suas respostas antes de prosseguir.
- **Aberto:** Open Model Thread Group nativo, 1 chegada por segundo durante 10 segundos, chegadas aleatórias e semente 42. A chegada de novas sessões independe do término das anteriores; a quantidade efetiva pode variar pela distribuição aleatória. Não requer plugins adicionais.

Cada iteração/sessão executa `POST /login`, extrai o token em variável exclusiva da thread, cadastra com `POST /pets` e consulta com `GET /pets`. Login usa JSON com `username` e `password`; pets usam `name` e `type`. O nome inclui UUID para evitar conflito. As operações protegidas enviam `Authorization: Bearer` com o token. Há pausa de 300 ms antes de cada operação de pets. Falhas interrompem a thread, evitando continuar com autenticação inválida.

Validações: login retorna 200 e token não vazio; cadastro retorna 201, identificador numérico, nome e tipo enviados; consulta retorna 200, lista JSON e contém o pet criado pela mesma thread. Não foi inventado um SLA de tempo de resposta.

## Executar no Mac

Requisitos: Node.js/npm e JMeter 5.6.3 com Java compatível. Clone a API oficial em uma pasta separada e instale as dependências:

```sh
git clone https://github.com/tiagotaq/api-de-pets.git
cd api-de-pets
npm ci --omit=dev --ignore-scripts
node -e "require('./rest/app').listen(3109,'127.0.0.1',()=>console.log('API local na porta 3109'))"
```

Mantenha esse terminal aberto. Em outro terminal, na pasta deste projeto, informe um usuário de estudo e a senha descritos no README da API oficial. As credenciais não ficam no plano nem nos arquivos versionados:

```sh
read 'PGATS_API_USER?Usuário de estudo da API: '
read -s 'PGATS_API_PASSWORD?Senha de estudo da API: '
echo
export PGATS_API_USER PGATS_API_PASSWORD
mkdir -p results
run_id=$(date +%Y%m%d-%H%M%S)
jmeter -n -t pets.jmx -l "results/$run_id.jtl" -j "results/$run_id.log" -e -o "results/$run_id-dashboard" -Jjmeterengine.nongui.port=0
unset PGATS_API_USER PGATS_API_PASSWORD
```

Esses comandos de leitura são para zsh, padrão do macOS. Para inspecionar o plano visualmente, abra `pets.jmx` no JMeter. Para executar pela interface com as variáveis, inicie `jmeter -t pets.jmx` no mesmo terminal após exportá-las. O VS Code pode abrir esta pasta para consulta e edição dos arquivos.

O alvo está fixado em `127.0.0.1`, porta padrão deste projeto 3109 (alterável com `-Jport=3000`). Execute apenas contra a cópia local de estudo. Reiniciar a API descarta os pets, pois o armazenamento é em memória. Não execute contra produção.

## Interpretação

Foi utilizada uma carga pequena para demonstrar os modelos aberto e fechado. A API e o JMeter foram executados localmente no mesmo computador. Os tempos dependem do ambiente, e os resultados não representam um teste de capacidade. Consulte `RESULTADOS.md` para a execução registrada.O relatório HTML pode ser gerado localmente pelo comando acima.
