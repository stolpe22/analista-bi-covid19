# Projeto ETL COVID-19

Este projeto foi desenvolvido como parte de um case teste para um processo seletivo. O objetivo é baixar e processar dados do repositório COVID-19 do [CSSEGISandData](https://github.com/CSSEGISandData/COVID-19) no GitHub e inseri-los em um banco de dados PostgreSQL. Além disso, o projeto inclui um dashboard em Power BI no formato .pbip, utilizando Direct Query para evitar problemas de tamanho ao subir dados importados no GitHub.

---

## Explicação do Notebook

Este notebook realiza o processo completo de ETL (Extract, Transform, Load) para os dados de COVID-19 do repositório CSSEGISandData no GitHub. Abaixo está a explicação detalhada de cada etapa:

### 1. Importação das Bibliotecas

No início, são importadas as bibliotecas necessárias para a execução do notebook, incluindo:

- `requests` e `aiohttp`: Para fazer requisições HTTP na API do GitHub.
- `pandas` e `numpy`: Para processamento e manipulação de dados.
- `dotenv`: Para carregar variáveis de ambiente, como o token do GitHub e as credenciais do banco de dados.
- `asyncio` e `tqdm.asyncio`: Para realizar downloads de forma assíncrona com barra de progresso.
- `psycopg2`: Para conectar e inserir dados no PostgreSQL.

### 2. Configuração de Variáveis para Requisição HTTP

As variáveis de configuração para acesso à API do GitHub são definidas utilizando o token armazenado no arquivo `.env`. Isso inclui:

- `GITHUB_TOKEN`: Token de autenticação para a API do GitHub.
- `REPO_OWNER` e `REPO_NAME`: Identificadores do repositório do GitHub.
- `API_URL` e `GITHUB_API_GRAPHQL_URL`: URLs para acesso às APIs REST e GraphQL do GitHub.

### 3. Teste de Limites de Requisição

Para evitar erros de limite de requisições na API do GitHub, são realizados dois testes:

- **REST API:** Verifica o limite de requisições usando o endpoint `/rate_limit`.
- **GraphQL API:** Executa uma consulta para verificar o limite e o tempo de reset.

### 4. Listagem de Arquivos no Repositório

Utiliza a API GraphQL para listar todos os arquivos CSV presentes no repositório, incluindo subpastas. Essa abordagem reduz o número de requisições necessárias em comparação com a API REST.

- A função `listar_arquivos_recursivo()` é usada para buscar os arquivos de forma recursiva.
- A lista é armazenada em um DataFrame `df_csvs` para melhor visualização.

### 5. Download Assíncrono dos Arquivos CSV

Para melhorar a eficiência do download, é utilizada a abordagem assíncrona com `aiohttp` e `asyncio`, permitindo baixar múltiplos arquivos simultaneamente.

- A função `baixar_arquivo()` baixa um único arquivo mantendo a estrutura de diretórios.
- `baixar_csvs_assincrono()` coordena o download de todos os arquivos listados.
- O progresso é exibido utilizando `tqdm`.

### 6. Processamento dos Arquivos CSV

Após o download, os arquivos são processados:

- A função `processar_csvs()` carrega cada arquivo CSV em um DataFrame e adiciona uma coluna `date` com a data do relatório extraída do nome do arquivo.
- Todos os DataFrames são concatenados em um único DataFrame `df_final`.

### 7. Salvamento em CSV

O DataFrame final é salvo em um novo arquivo `daily_reports_concat.csv`, armazenado na pasta `files/final`.

- Para evitar problemas de memória, o arquivo é salvo em chunks de 10.000 linhas, exibindo o progresso com `tqdm`.

### 8. Tratamento de Dados

Antes de inserir no banco de dados, é feito o tratamento dos dados:

- A função `tratar_dados()` remove valores NaN e faz a conversão de datas para o formato correto.
- As colunas de datas são convertidas usando `pd.to_datetime()`.

### 9. Inserção dos Dados no PostgreSQL

Os dados são inseridos no banco de dados PostgreSQL utilizando `psycopg2`:

- A função `inserir_dados()` cria a tabela `covid_data` caso não exista.
- Em seguida, os dados são inseridos linha por linha, exibindo o progresso.
- O uso de `cur.execute()` com `sql.SQL()` garante segurança contra injeção de SQL.

### Observações Adicionais

- O uso de `.env` permite que o código seja executado em diferentes ambientes sem expor credenciais sensíveis.
- A abordagem assíncrona reduz consideravelmente o tempo necessário para o download dos arquivos CSV.

## Funcionalidades

- **Download dos dados:** Realiza o download dos relatórios diários de COVID-19 diretamente do repositório do GitHub.
- **Processamento de dados:** Concatena os arquivos CSV em um único DataFrame e adiciona uma coluna com a data do relatório.
- **Tratamento de dados:** Realiza tratamento de NaNs e conversão de tipos de dados.
- **Carga de dados:** Insere os dados processados no banco de dados PostgreSQL.
- **Dashboard em Power BI:** Um dashboard no formato .pbip utilizando Direct Query para consulta em tempo real dos dados no banco PostgreSQL.

---

## Pré-requisitos

- Python 3.8+
- PostgreSQL
- Power BI (para abrir o arquivo .pbip na pasta `pbip_dashboard`)

### Bibliotecas Python necessárias

- `requests`
- `pandas`
- `dotenv`
- `aiohttp`
- `asyncio`
- `tqdm`
- `psycopg2`
- `numpy`

Você pode instalar todas as dependências utilizando o comando:

```sh
pip install -r requirements.txt
```

---

## Configuração do GitHub Token

Para acessar a API do GitHub, você precisará de um token de acesso pessoal.

1. Vá para [Configurações do GitHub](https://github.com/settings/tokens) e clique em "Generate new token".
2. Selecione as permissões necessárias, como `repo` e `read:org`.
3. Copie o token gerado.
4. Crie um arquivo `.env` na raiz do projeto com o seguinte conteúdo:

```env
GITHUB_TOKEN=seu_token_aqui
DB_NAME=nome_do_banco
DB_USER=usuario
DB_PASSWORD=senha
DB_HOST=localhost
DB_PORT=5432
```

Para mais detalhes sobre como gerar um token no GitHub, consulte a [documentação oficial](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

---

## Configuração do Banco de Dados PostgreSQL

1. Crie um banco de dados no PostgreSQL utilizando o comando:

```sql
CREATE DATABASE nome_do_banco;
```

2. Insira as credenciais no arquivo `.env` como mostrado acima.

---

## Execução do Projeto

1. Baixe os arquivos CSV do repositório do GitHub:
2. Os arquivos serão salvos na pasta `files` com a estrutura de diretórios original.
3. O script processará os dados e inserirá no banco de dados PostgreSQL.

---

## Dashboard Power BI

O projeto inclui um dashboard Power BI na pasta `pbip_dashboard`:

- Formato: `.pbip`
- Tipo de conexão: Direct Query com o PostgreSQL

Abra o arquivo com o Power BI para visualizar as análises.

---

## Observações

- O arquivo `.pbip` utiliza Direct Query para acessar os dados em tempo real. Para garantir a funcionalidade, o banco deve estar em execução e acessível pelo Power BI.
