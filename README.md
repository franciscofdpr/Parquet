# Processo de Transformação e Carregamento de CSV para Parquet e DuckDB

Este projeto descreve um processo simples de conversão de um arquivo CSV para o formato Parquet e como carregá-lo em um banco de dados DuckDB para consultas e análise de dados.

## 1. Objetivo

O objetivo deste processo é:
- Carregar um arquivo CSV para um DataFrame do **Pandas**.
- Salvar o arquivo como um arquivo **Parquet**, que é um formato de armazenamento eficiente.
- Carregar o arquivo Parquet em uma tabela de banco de dados **DuckDB** para realizar consultas.

### Ferramentas Utilizadas
- **Pandas**: Para manipulação de dados e conversão de formatos (CSV para Parquet).
- **DuckDB**: Banco de dados SQL para análise de dados em arquivos Parquet e outros formatos.

## 2. Estrutura do Código

O código está dividido em três partes principais:

### 2.1. Transformando CSV em Parquet

Primeiramente, o arquivo CSV é lido usando a biblioteca **Pandas**. O arquivo é então convertido e salvo no formato Parquet.

```python
import pandas as pd

# Tente carregar o CSV com delimitador ';' (muito comum em arquivos CSV no Brasil)
df = pd.read_csv('C:/Users/RibeiroF/Downloads/nominatim.csv', sep=';')

# Verifique as primeiras linhas para conferir se a leitura deu certo
print(df.head())

# Salvar como Parquet
df.to_parquet('C:/Users/RibeiroF/Downloads/nominatim.parquet', engine='pyarrow')
```

### 2.2. Lendo o Arquivo Parquet com DuckDB

Depois de converter o CSV em Parquet, o próximo passo é carregar e consultar esse arquivo usando o **DuckDB**. Abaixo está o código que se conecta ao banco de dados e executa uma consulta no arquivo Parquet.

```python
import duckdb

# Connect to DuckDB (in-memory database)
conn = duckdb.connect()

# Query a Parquet file
result = conn.execute("SELECT * FROM 'C:/Users/RibeiroF/Downloads/nominatim.parquet' LIMIT 10").fetchall()

# Display the results
for row in result:
    print(row)

# Close the connection
conn.close()
```

### 2.3. Gravando o Arquivo Parquet em uma Tabela DuckDB

Aqui, o arquivo Parquet é carregado diretamente em uma tabela dentro do banco de dados DuckDB, permitindo consultas SQL eficientes no conjunto de dados.

```python
import duckdb

# Conectar ao banco de dados (pode ser em memória ou em um arquivo físico)
conn = duckdb.connect(database='C:/Users/RibeiroF/Downloads/teste.duckdb')

# Criar uma tabela no DuckDB para armazenar os dados
conn.execute("""
    CREATE TABLE nominatim_table AS
    SELECT * FROM 'C:/Users/RibeiroF/Downloads/nominatim.parquet';
""")

# Verificar se a tabela foi criada corretamente
result = conn.execute("SELECT * FROM nominatim_table LIMIT 5").fetchall()

# Exibir os primeiros 5 registros
for row in result:
    print(row)

# Fechar a conexão
conn.close()
```

## 3. Como Executar

Para utilizar este processo, siga os seguintes passos:

1. **Instalar as dependências**:
   - **Pandas** para ler e salvar arquivos em diferentes formatos:
     ```bash
     pip install pandas
     ```
   - **DuckDB** para consultar arquivos Parquet e interagir com bancos de dados:
     ```bash
     pip install duckdb
     ```

2. **Preparar o arquivo CSV**:
   - Coloque o arquivo `nominatim.csv` no diretório desejado ou altere o caminho no código conforme necessário.
   - Certifique-se de que o arquivo CSV use o delimitador correto, que no caso do Brasil é muitas vezes o ponto e vírgula `;`.

3. **Rodar o script**:
   - Execute o código para ler o CSV, salvar o arquivo Parquet e carregá-lo no DuckDB.

## 4. Explicação dos Passos

### 4.1. Leitura e Conversão do CSV

- O **Pandas** lê o arquivo CSV e o armazena em um DataFrame.
- O arquivo CSV pode ser grande, e ao transformá-lo para **Parquet**, você reduz significativamente o espaço em disco, além de acelerar operações de leitura e escrita.

### 4.2. Leitura do Arquivo Parquet com DuckDB

- O **DuckDB** permite consultar o arquivo Parquet diretamente sem necessidade de carregá-lo totalmente em memória, o que é altamente eficiente.
- O código executa uma consulta simples (`LIMIT 10`) para visualizar os dados.

### 4.3. Carregando Parquet para uma Tabela DuckDB

- O arquivo Parquet é carregado em uma tabela DuckDB, o que permite consultas SQL diretamente sobre os dados, sem a necessidade de pré-processamento ou transformação adicional.

## 5. Considerações Finais

Este processo é útil quando você precisa lidar com grandes volumes de dados de forma eficiente, tanto em termos de espaço em disco quanto em termos de velocidade de leitura e consulta. O formato Parquet, combinado com DuckDB, oferece uma solução poderosa para análise de dados sem a necessidade de um sistema de banco de dados tradicional.

## 6. Licença

Este projeto é disponibilizado sob a [MIT License](https://opensource.org/licenses/MIT).
