# Data Engineer Project: Dbt Data Pipeline

*Using Airflow, BigQuery, Google Cloud Storage, dbt, Soda, Metabase and Python*

## Objective
O objetivo deste projeto é criar um pipeline de dados ponta a ponta a partir de um conjunto de dados Kaggle sobre dados de varejo. Isso envolve a modelagem dos dados em tabelas de dimensão de fatos, a implementação de etapas de qualidade de dados, a utilização de tecnologias modernas de pilha de dados (dbt, Soda e Airflow) e o armazenamento dos dados na nuvem (Google Cloud Platform). Por fim, a camada de relatórios é consumida no Metabase. O projeto é conteinerizado via Docker e versionado no GitHub.
- Pilha de Tecnologia Utilizada
- Python
- Docker e Docker-compose
- Soda.io
- Metabase
- Google Cloud Storage
- Google BigQuery
- Airflow (versão Astronomer)
- dbt
- GitHub (Repositório)

Na pasta ```dags/include/datasets/``` você encontrará 3 arquivos: o ```online_retail.csv``` é o arquivo original baixado do Kaggle e o ```country.csv``` foi gerado usando uma tabela do BigQuery. São todos os dados necessários para este projeto.

---------
## Para executar o Projeto:

### Instalação Docker
[Install Docker for your OS](https://docs.docker.com/desktop/)

### Instalação Astro CLI
[Install Astro CLI for your OS](https://www.astronomer.io/docs/astro/cli/install-cli)

### Reinicializar o Airflow
Open the code editor terminal:
```bash
astro dev init
```
Atenção a pergunta apresentada: It will ask: ```You are not in an empty directory. Are you sure you want to initialize a project? (y/n)```
Type ```y``` and the project will be reinitialized.


### Criando o Projeto com Astro
In the code editor terminal, type:

```bash
astro dev start
```
The default Airflow endpoint is http://localhost:8080/
- Default username: admin
- Default password: admin

### Criand o projeto GCP

Acesse https://console.cloud.google.com/ e vá até create a project

Atenção: Copie o ID do seu projeto e salve-o para mais tarde.

#### Using the project ID from GCP

Change the following files:
- .env (GCP_PROJECT_ID)
- include\dbt\models\sources\sources.yml (database)
- include\dbt\profiles.yml (project)

#### Create a Bucket on GCP

Com o projeto selecionado, acesse https://console.cloud.google.com/storage/browser e crie um Bucket.
Use o nome ```<seunome>_online_retail```.
E altere o valor da variável ```bucket_name``` para o nome do seu bucket no arquivo ```dags\retail.py```.

#### Create an service account for the project

Acesse a aba IAM e crie a conta de serviço com o nome ```airflow-online-retail```.
Conceda acesso de administrador ao GCS e ao BigQuery e exporte as chaves JSON. Renomeie o arquivo para service_account.json e coloque-o dentro da pasta ```include/gcp/``` (você precisará criar esta pasta).

#### Build a connection in your airflow

No seu fluxo de Airflow, em http://localhost:8080/, faça login e acesse Admin → Conexões.
Crie uma nova conexão e use estas configurações:
- id: gcp
- type: Google Cloud
- Keypath: `/usr/local/airflow/include/gcp/service_account.json`

Salve.

### Create you SODA account and API Keys

Acesse https://www.soda.io/, clique em "Iniciar um teste" e crie uma conta. Em seguida, faça login, acesse seu perfil, Chaves de API e crie uma nova chave de API.

Copie o código soda_cloud, que ficará assim:
```
soda_cloud:
host: cloud.us.soda.io
api_key_id: <CHAVE>
api_key_secret: <SEGREDO>
```
E cole-o em ```include\soda\configuration.yml``` ou edite o arquivo .env com os respectivos valores.
Observe que, neste exemplo, a conta criada estava na região dos EUA. Se a sua conta estiver na região da UE, você terá que alterar a variável "host".

### All set, start the DAG

Com o Airflow em execução, acesse http://localhost:8080/, clique em DAGs e, em seguida, no DAG de varejo.
Em seguida, inicie o DAG (botão de reprodução no canto superior direito).

Ele será executado passo a passo e, se tudo for seguido, você verá uma execução verde no final.
Verifique na sua conta do GCP Storage se o arquivo foi carregado com sucesso, na sua aba do BigQuery se as tabelas foram criadas e no seu painel do Soda se tudo está correto.

Em seguida, vá para o Metabase e crie seu próprio painel. O serviço do Metabase está em http://localhost:3000/


### References
Project based on [Marc Lamberti](https://www.youtube.com/@MarcLamberti) videos.

