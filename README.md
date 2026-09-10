# Pandas — Transformando e Manipulando Dados

Repositório de estudos práticos com **Python, Pandas e NumPy**, focado na transformação, normalização, limpeza e manipulação de dados estruturados.

O projeto utiliza dados relacionados a **hospedagens, imóveis e disponibilidade de imóveis** para demonstrar, em um Jupyter Notebook, etapas comuns encontradas no trabalho de um **Analista de Dados** e como base para práticas de **Engenharia de Dados**.

O notebook principal trabalha desde a leitura de dados JSON até a transformação de estruturas aninhadas em tabelas, conversão de tipos, tratamento de valores monetários, limpeza textual e análise de dados temporais.

---

## Sobre o projeto

O principal material deste repositório é o notebook:

```text
analise_dados_moveis_e_hospedagem.ipynb
```

O notebook começa carregando dados de hospedagem a partir de um arquivo JSON e transforma uma estrutura inicialmente aninhada em um DataFrame tabular.

Durante o processo são aplicadas diversas operações do Pandas e NumPy para transformar os dados em uma estrutura mais adequada para análise.

O projeto também contém um conjunto separado de dados para um desafio prático.

---

## Objetivos de aprendizagem

O projeto foi desenvolvido para praticar:

- Leitura de arquivos JSON;
- Criação e exploração de DataFrames;
- Normalização de dados JSON;
- Expansão de listas em linhas;
- Conversão de tipos de dados;
- Tratamento de valores monetários;
- Limpeza e padronização de textos;
- Utilização de expressões regulares;
- Manipulação de strings;
- Conversão de datas;
- Extração de informações de datas;
- Agrupamento de dados;
- Agregação de informações;
- Análise de disponibilidade de imóveis;
- Organização de dados para análises posteriores.

---

# Tecnologias utilizadas

## Python

Linguagem utilizada para desenvolvimento do notebook e execução das transformações.

A versão do Python registrada no notebook é **3.13.1**.

Para maior flexibilidade e compatibilidade, recomenda-se utilizar Python **3.11 ou superior**.

---

## Pandas

Principal biblioteca utilizada no projeto.

O Pandas fornece as estruturas `Series` e `DataFrame` e as operações necessárias para leitura, transformação, limpeza e análise dos dados.

Principais recursos utilizados no projeto:

```python
pd.read_json()
pd.json_normalize()
DataFrame.explode()
DataFrame.astype()
DataFrame.apply()
DataFrame.info()
DataFrame.head()
pd.to_datetime()
DataFrame.groupby()
```

---

## NumPy

Utilizado principalmente para conversão explícita de tipos numéricos.

Exemplos utilizados no notebook:

```python
np.int64
np.float64
```

O NumPy é utilizado em conjunto com o Pandas para garantir tipos numéricos adequados durante o processo de transformação.

---

## Jupyter Notebook

O projeto é desenvolvido em formato `.ipynb`, permitindo executar o código de maneira interativa e acompanhar os resultados de cada etapa da transformação.

O notebook também possui metadados compatíveis com execução no **Google Colab**.

---

## Git e GitHub

Utilizados para versionamento, organização e compartilhamento do projeto.

---

# Fluxo de transformação dos dados

O processo realizado no notebook pode ser representado da seguinte maneira:

```text
                 JSON
                  │
                  ▼
           Pandas read_json()
                  │
                  ▼
        Estrutura aninhada
                  │
                  ▼
        pd.json_normalize()
                  │
                  ▼
             DataFrame
                  │
                  ▼
             explode()
                  │
                  ▼
       Dados em formato tabular
                  │
        ┌─────────┼──────────┐
        │         │          │
        ▼         ▼          ▼
     Tipagem   Limpeza    Strings
        │         │          │
        └─────────┼──────────┘
                  │
                  ▼
          Dados preparados
                  │
                  ▼
        Datas / Agrupamentos
                  │
                  ▼
              Análise
```

Esse fluxo representa uma etapa importante de um processo de **ETL (Extract, Transform, Load)**:

- **Extract:** leitura dos dados JSON;
- **Transform:** normalização, expansão, limpeza e conversão;
- **Load:** neste projeto, o foco está principalmente na transformação e preparação dos dados para análise.

---

# Dataset de hospedagem

O arquivo:

```text
dados/dados_hospedagem.json
```

contém informações de hospedagens.

Após a normalização, são trabalhadas informações como:

- avaliação geral;
- experiência local;
- quantidade máxima de hóspedes;
- descrição do imóvel;
- descrição da vizinhança;
- quantidade de banheiros;
- quantidade de quartos;
- quantidade de camas;
- modelo de cama;
- comodidades;
- taxa de depósito;
- taxa de limpeza;
- preço.

O notebook inicialmente apresenta dados aninhados e posteriormente os transforma em uma estrutura tabular.

---

# Normalização de dados JSON

Uma das etapas mais importantes é transformar a estrutura aninhada utilizando:

```python
dados = pd.json_normalize(dados['info_moveis'])
```

Essa operação converte os objetos presentes na coluna `info_moveis` em colunas de um DataFrame.

Antes da normalização, uma linha pode conter diversas informações agrupadas em um único objeto.

Depois da normalização:

```text
Objeto JSON
     │
     ▼
json_normalize()
     │
     ▼
DataFrame estruturado
```

Esse conceito é particularmente importante em pipelines de dados porque APIs e arquivos JSON frequentemente apresentam estruturas aninhadas.

---

# Expansão de listas com explode()

Após a normalização, algumas colunas ainda possuem listas.

O notebook utiliza:

```python
dados = dados.explode(colunas[3:])
```

O `explode()` transforma elementos de listas em linhas individuais.

Exemplo conceitual:

```text
Antes:

imovel | quarto
-------|--------------------
A      | [quarto 1, quarto 2]

Depois:

imovel | quarto
-------|--------
A      | quarto 1
A      | quarto 2
```

Essa transformação permite trabalhar com os elementos individualmente.

No notebook, a transformação expande os dados para milhares de registros, deixando a estrutura mais adequada para análise.

---

# Conversão de tipos

Depois da transformação estrutural, os dados passam por uma etapa de tipagem.

Exemplos:

```python
dados['max_hospedes'] = dados['max_hospedes'].astype(np.int64)
```

```python
dados['avaliacao_geral'] = dados['avaliacao_geral'].astype(np.float64)
```

E também:

```python
dados[col_numeric] = dados[col_numeric].astype(np.int64)
```

Essa etapa é importante porque dados importados de arquivos podem ser carregados inicialmente como `object`, mesmo quando representam números.

A tipagem correta permite realizar cálculos e análises de forma adequada.

---

# Tratamento de valores monetários

Os valores monetários inicialmente possuem símbolos e separadores, por exemplo:

```text
$1,000.00
$350.00
$99.00
```

O notebook realiza a limpeza desses valores:

```python
dados['preco'] = dados['preco'].apply(
    lambda x: x.replace('$', '').replace(',', '').strip()
)
```

Depois os valores são convertidos para `float64`:

```python
dados['preco'] = dados['preco'].astype(np.float64)
```

O mesmo processo é aplicado às colunas:

```text
taxa_deposito
taxa_limpeza
```

Esse procedimento transforma valores originalmente textuais em dados numéricos que podem ser utilizados em cálculos.

---

# Limpeza de texto

O notebook também trabalha com limpeza e padronização de textos.

Exemplo de transformação para letras minúsculas:

```python
dados['descricao_local'] = dados['descricao_local'].str.lower()
```

Também são utilizadas expressões regulares para remover caracteres específicos:

```python
dados['descricao_local'] = dados['descricao_local'].str.replace(
    r"[^a-zA-Z0-9\-']",
    '',
    regex=True
)
```

E posteriormente:

```python
dados['descricao_local'] = dados['descricao_local'].str.split()
```

As comodidades também passam por limpeza:

```python
dados['comodidades'] = dados['comodidades'].str.replace(
    r"\{|}|\"",
    '',
    regex=True
)
```

Essas operações demonstram conceitos importantes de **Data Cleaning** e preparação de dados textuais.

---

# Análise temporal

O segundo conjunto de dados utilizado no notebook é:

```text
dados/moveis_disponiveis.json
```

Ele contém informações relacionadas à disponibilidade de imóveis ao longo do tempo.

O notebook apresenta um DataFrame com:

```text
id
data
vaga_disponivel
preco
```

A coluna `data` inicialmente é convertida para o tipo datetime:

```python
dt_data['data'] = pd.to_datetime(dt_data['data'])
```

Depois é possível extrair o ano e mês:

```python
dt_data['data'].dt.strftime('%Y-%m')
```

Isso permite transformar uma data como:

```text
2016-01-04
```

em:

```text
2016-01
```

---

# Agrupamento e agregação

Após a conversão das datas, os dados podem ser agrupados por mês.

O notebook utiliza:

```python
subset = dt_data.groupby(
    dt_data['data'].dt.strftime('%Y-%m')
)['vaga_disponivel'].sum()
```

Essa operação permite obter a quantidade de registros disponíveis por período.

Conceitualmente:

```text
Data completa
     │
     ▼
Ano-Mês
     │
     ▼
GroupBy
     │
     ▼
Agregação
     │
     ▼
Disponibilidade por período
```

Esse tipo de operação é bastante comum em análises de séries temporais e indicadores de negócio.

---

# Desafio

O diretório:

```text
desafio/
```

contém conjuntos de dados adicionais para prática.

Estrutura:

```text
desafio/
├── dados_locacao_imoveis.json
└── dados_vendas_clientes.json
```

Esses arquivos podem ser utilizados para reproduzir e ampliar as técnicas apresentadas no notebook.

Possíveis exercícios:

- leitura dos JSON;
- normalização;
- identificação de estruturas aninhadas;
- limpeza;
- conversão de tipos;
- exploração dos dados;
- criação de métricas;
- agrupamentos;
- análises por categoria;
- preparação para uma etapa posterior de visualização ou ETL.

---

# Estrutura do projeto

```text
pandas-transformando-e-manipulando-dados/
│
├── dados/
│   ├── dados_hospedagem.json
│   └── moveis_disponiveis.json
│
├── desafio/
│   ├── dados_locacao_imoveis.json
│   └── dados_vendas_clientes.json
│
├── analise_dados_moveis_e_hospedagem.ipynb
│
├── requirements.txt
│
└── README.md
```

---

# Como executar o projeto

## Pré-requisitos

Recomenda-se ter instalado:

- Python 3.11 ou superior;
- Git;
- Jupyter Notebook ou JupyterLab.

Também é possível executar o notebook utilizando o Google Colab.

---

# 1. Clonar o repositório

```bash
git clone https://github.com/TheGabrielVieira/pandas-transformando-e-manipulando-dados.git
```

Entre no diretório:

```bash
cd pandas-transformando-e-manipulando-dados
```

---

# 2. Criar um ambiente virtual

É recomendado utilizar um ambiente virtual para manter as dependências isoladas.

## Windows

```bash
python -m venv .venv
```

Ative:

```bash
.venv\Scripts\activate
```

## Linux / macOS

```bash
python3 -m venv .venv
```

Ative:

```bash
source .venv/bin/activate
```

---

# 3. Instalar as dependências

Com o ambiente virtual ativado:

```bash
python -m pip install --upgrade pip
```

Depois:

```bash
pip install -r requirements.txt
```

O `requirements.txt` contém as bibliotecas necessárias para executar o notebook.

---

# 4. Executar o Jupyter Notebook

Execute:

```bash
jupyter notebook
```

ou:

```bash
jupyter lab
```

Depois abra:

```text
analise_dados_moveis_e_hospedagem.ipynb
```

Execute as células sequencialmente.

---

# Utilizando VS Code

O projeto também pode ser executado no Visual Studio Code.

Recomenda-se instalar:

- extensão Python;
- extensão Jupyter.

Depois de abrir o projeto:

```bash
code .
```

Selecione como interpretador o Python localizado no ambiente:

```text
.venv
```

Abra o notebook:

```text
analise_dados_moveis_e_hospedagem.ipynb
```

e selecione o kernel correspondente ao ambiente virtual.

---

# Google Colab

O notebook possui metadados compatíveis com Google Colab.

Para executar no Colab, abra o arquivo:

```text
analise_dados_moveis_e_hospedagem.ipynb
```

e carregue o notebook no ambiente do Google Colab.

Caso o notebook seja executado fora da estrutura original do repositório, mantenha a pasta `dados/` disponível no diretório de trabalho, pois os arquivos JSON são referenciados por caminhos relativos.

---

# Dependências

O projeto utiliza as seguintes dependências:

| Tecnologia | Finalidade |
|---|---|
| Python | Linguagem de programação |
| Pandas | Manipulação, transformação e análise de dados |
| NumPy | Operações numéricas e conversão de tipos |
| Jupyter | Execução interativa do notebook |

O arquivo `requirements.txt` foi criado para permitir a instalação das dependências com um único comando:

```bash
pip install -r requirements.txt
```

---

# Principais conceitos de Pandas praticados

| Conceito | Aplicação |
|---|---|
| `read_json()` | Importação dos datasets |
| `json_normalize()` | Normalização de JSON aninhado |
| `DataFrame` | Estrutura principal de dados |
| `head()` | Inspeção inicial |
| `info()` | Análise de tipos e valores não nulos |
| `explode()` | Expansão de listas em linhas |
| `astype()` | Conversão de tipos |
| `apply()` | Aplicação de funções aos dados |
| `.str` | Manipulação de strings |
| `str.replace()` | Limpeza textual |
| `str.split()` | Separação de texto |
| `to_datetime()` | Conversão para datas |
| `.dt` | Operações sobre datas |
| `groupby()` | Agrupamento e agregação |

---

# Relação com Análise de Dados

Este projeto representa uma etapa importante do fluxo de trabalho de um Analista de Dados.

Antes de gerar indicadores, gráficos ou modelos, os dados normalmente precisam ser:

```text
Coletados
   ↓
Inspecionados
   ↓
Normalizados
   ↓
Limpos
   ↓
Tipados
   ↓
Transformados
   ↓
Agregados
   ↓
Analisados
```

O foco deste repositório está principalmente nas etapas de:

**Inspeção → Normalização → Limpeza → Transformação → Preparação → Análise**

---

# Relação com Engenharia de Dados

Embora o projeto seja apresentado como um estudo de Pandas, os conceitos praticados são diretamente relacionados a tarefas de Engenharia de Dados.

Especialmente:

- ingestão de dados JSON;
- transformação de estruturas semiestruturadas;
- normalização;
- conversão de tipos;
- limpeza;
- padronização;
- preparação de dados;
- agregação.

Essas operações podem posteriormente ser incorporadas em pipelines de ETL/ELT mais completos.

Um possível próximo passo seria transformar o notebook em um pipeline:

```text
JSON
 │
 ▼
Extract
 │
 ▼
Transform
 ├── Normalize
 ├── Clean
 ├── Type conversion
 └── Validation
 │
 ▼
Load
 │
 ▼
CSV / Database / Data Warehouse
```

---

# Melhorias futuras

Algumas evoluções possíveis para transformar este material de estudo em um projeto de portfólio mais próximo de um pipeline profissional:

- [ ] Criar um pipeline ETL em Python;
- [ ] Separar as etapas em scripts `.py`;
- [ ] Criar funções reutilizáveis para limpeza;
- [ ] Adicionar validação de dados;
- [ ] Criar logs de execução;
- [ ] Utilizar `pathlib` para gerenciamento de caminhos;
- [ ] Adicionar testes automatizados;
- [ ] Exportar os dados transformados para CSV;
- [ ] Persistir os dados em SQLite ou PostgreSQL;
- [ ] Criar uma camada de análise;
- [ ] Criar visualizações;
- [ ] Documentar métricas e regras de transformação;
- [ ] Automatizar a execução com uma ferramenta de orquestração.

---

# Referências

- [Documentação oficial do Pandas](https://pandas.pydata.org/docs/)
- [Pandas — JSON I/O](https://pandas.pydata.org/docs/user_guide/io.html#json)
- [Pandas — `json_normalize`](https://pandas.pydata.org/docs/reference/api/pandas.json_normalize.html)
- [Pandas — `explode`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.explode.html)
- [Documentação oficial do NumPy](https://numpy.org/doc/)

---

# Autor

**Gabriel Vieira**

GitHub:

https://github.com/TheGabrielVieira

Este repositório faz parte dos estudos práticos em:

- Python;
- Pandas;
- Análise de Dados;
- Engenharia de Dados;
- ETL;
- Manipulação e transformação de dados.

---

# Licença

Projeto desenvolvido para fins educacionais e de estudo.
