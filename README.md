# Projeto Engenharia de Dados: Pipeline completo utilizando Airflow, AWS, Snowflake, dbt e Looker Studio

![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/8ef01e99-4399-4975-a060-4e63b504761a)

## Objetivo

Neste projeto utilizei um banco de dados "vivo" (atualizado constantemente) de uma empresa fictícia de venda de veículos para desenvolver um pipeline completo, começando pela extração, transformação até a visualização dos dados, utilizando ferramentas como Airflow, AWS (EC2), Snowflake, dbt e Looker Studio. 

## Etapas

### 1. Verificação da base de dados 
   - Acesso ao banco de dados da empresa utilizando o pgAdmin4 (PostgreSQL)

### 2. Criação de uma máquina virtual EC2 (AWS) e na sequência criação do Airflow neste ambiente
   - Criei uma VM em EC2 tipo t2.large e sistema operacional Ubuntu.
   - Utilizando cliente ssh e o terminal do meu computador, conectei a instância criada na AWS e instalei o Docker e o Airflow.
   - Através do endereço público da instância e porta configurada para receber a interface gráfica, obtive acesso à plataforma do Airflow para configuração.

### 3. Extração de dados para o Snowflake utilizando o Airflow 
   - Criei uma conta no Snowflake.
   - Utilizando comandos em SQL no Snowflake, criei todo o ambiente para receber os dados, ou seja, o banco de dados, o schema (stage), warehouse e tabelas necessárias.
   - Criação da DAG. Serão duas tasks por tabela: "get_max_id" e "load_incremental_data", pois no processo de carga incremental de dados devemos carregar a partir da última chave.
   - Realizei as conexões do Postgres e Snowflake no Airflow.
   - Publiquei e rodei a DAG no Airflow.
   - Realizei algumas consultas SQL no Snowflake para testar se os dados foram enviados para lá. Após confirmar que os dados foram enviados com sucesso, testei também se os registros incrementais também estão sendo enviados. Registros incrementais também ok.

### 4. Transformação dos dados no Snowflake com o DBT 
   - Utilizei o dbt para transformar os dados dentro do DW.
   - A ideia para esta etapa foi criar uma camada analítica do banco de dados para que usuários (analistas e cientistas de dados, gestores, etc) possam consultar os dados. Portanto dupliquei a camada origem criando a stage, para que haja um desacoplamento dos dados originais e não comprometê-los. A partir da stage, criei as camadas/tabelas dimensões, a fato e as análises.
     
![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/795442eb-dc7b-45a1-a6c8-0f1713816eb5)

![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/8291dbf9-741a-4838-9c18-2b22f86c1ab6)

   - Configurei o dbt fazendo a conexão com o Snowflake, criei uma nova branch de desenvolvimento e configurei o arquivo source.
   - Na camada de análise fiz alguns processos de transformação para atender alguns requisitos de negócio, relacionando as tabelas das dimensões dimensões e fato.

![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/57e8f99b-1dba-4df1-a857-5a0271ba3220)

   - Linhagem completa dos dados a partir da visualização da linhagem da tabela fct_vendas:

![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/13ce24bd-570c-4408-9a95-8c9b7a2a875d)

   - Verifiquei que os dados foram corretamente para o Snowflake.

### 5. Testes utilizando o DBT e deploy

   - O teste aplicado foi baseado na regra de negócio "Um veículo não pode ser vendido acima do preço de tabela e o valor máximo de desconto é de 5%". Portanto criei um arquivo na pasta "tests" com o seguinte código SQL:

![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/f4f9f5e6-6db7-4469-b1f5-34941b636f33)

   - O teste passou com sucesso.
   - Realizei o merge das branches.
   - Criei um novo Environment para fazer o deploy.
   - Criei um novo job na Environment de deploy e executei.
   - Job executado com sucesso:
     
![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/081d9a6c-ba0d-481b-963e-3dea863f5105)

   - Gerei os documentos do projeto.  

### 6. Visualização dos dados: criação de um dashboard com o Looker Studio.

   - Embora o foco do projeto não seja exatamente a visualização dos dados e sim as etapas realizadas anteriormente, optei por criar um dashboard simples no Looker Studio para visualização de alguns dados do projeto.  
   - Configurei o Looker Studio para conectá-lo ao Snowflake.
   - Dashboard realizado:
     
![image](https://github.com/rexpires/Pipeline_completo/assets/105373494/9ed8a7e1-3203-488a-88a3-9dd6a6dd5c64)

