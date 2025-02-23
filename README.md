# COVID-19 Data Pipeline

Este projeto faz a extração, processamento e carga de dados sobre COVID-19 do repositório [CSSEGISandData/COVID-19](https://github.com/CSSEGISandData/COVID-19) para um banco de dados PostgreSQL.

## Funcionalidades

- Verificação de limite de requisições da API do GitHub
- Listagem recursiva de todos os arquivos `.csv` do repositório usando GraphQL
- Download assíncrono dos arquivos utilizando `aiohttp`
- Processamento e concatenação dos arquivos `.csv` em um DataFrame do Pandas
- Transformação e limpeza dos dados
- Carga dos dados transformados em um banco de dados PostgreSQL

## Pré-requisitos

- Python 3.x
- PostgreSQL
- GitHub Token (com permissões de leitura para repositórios públicos)

## Instalação

1. Clone o repositório:

   ```bash
   git clone https://github.com/seu-usuario/seu-repositorio.git
   cd seu-repositorio
   ```

2. Crie um ambiente virtual:

   ```bash
   python -m venv venv
   source venv/bin/activate  # Para Linux/Mac
   .\venv\Scripts\activate  # Para Windows
   ```

3. Instale as dependências:
   ```bash
   pip install -r requirements.txt
   ```

## Configuração do GitHub Token

O GitHub Token é necessário para acessar a API GraphQL. Siga os passos abaixo para criar e configurar o seu token:

1. Vá para [GitHub Settings](https://github.com/settings/tokens) e clique em **Generate new token**.
2. Defina um nome para o token e conceda a permissão `public_repo`.
3. Copie o token gerado.
4. Crie um arquivo `.env` na raiz do projeto com o seguinte conteúdo:
   ```env
   GITHUB_TOKEN=seu_token_aqui
   DB_HOST=seu_host
   DB_NAME=seu_banco
   DB_USER=seu_usuario
   DB_PASSWORD=sua_senha
   ```

## Configuração do PostgreSQL

Antes de executar o pipeline, certifique-se de ter um banco de dados PostgreSQL configurado. No arquivo `.env`, ajuste as variáveis de acordo com sua configuração do PostgreSQL:

```env
DB_HOST=localhost
DB_NAME=nome_do_banco
DB_USER=usuario_do_banco
DB_PASSWORD=senha_do_banco
```

## Executando o Projeto

```bash
jupyter notebook
```

Abra o arquivo `.ipynb` e execute as células na ordem para:

1. Verificar o limite de requisições da API do GitHub
2. Listar e baixar os arquivos `.csv`
3. Processar e transformar os dados
4. Inserir os dados no PostgreSQL

## Estrutura do Projeto

```
.
├── files/                      # Diretório para armazenar os arquivos baixados
│   └── csse_covid_19_data/      # Arquivos CSV organizados por pastas
├── requirements.txt             # Dependências do projeto
├── main.ipynb                   # Notebook principal do pipeline
└── README.md                    # Documentação do projeto
```

## Dependências

As dependências do projeto estão listadas no arquivo `requirements.txt`:

```
requests
pandas
python-dotenv
aiohttp
asyncio
tqdm
psycopg2
```
