# Análise Exploratória do Mercado Imobiliário em Goiânia

Projeto da disciplina **Introdução à Ciência de Dados** — Prof. Murilo Cabral
PUC Goiás · Ciência de Dados e Inteligência Artificial

**Autores:** Matheus de Melo Sari, João Paulo, Arthur Cabral

> **Status:** Fase 1 (Análise Exploratória de Dados).

---

## 1. Objetivo

Realizar a análise exploratória (EDA) de um conjunto de anúncios de imóveis de Goiânia, diagnosticando a qualidade dos dados e preparando a base para as análises seguintes.

Nesta fase foram feitos:

- Visão geral do dataset (estrutura, tipos, amostras)
- Diagnóstico de qualidade (valores ausentes, duplicados e únicos)
- Limpeza e conversão de tipos
- Tratamento de valores ausentes
- Exportação da base tratada

## 2. Dados

| Item | Descrição |
|---|---|
| Arquivo | `2021-08-05-all.csv` |
| Fonte | [Kaggle — Imóveis Goiânia](https://www.kaggle.com/datasets/williamu32/imoveis-goiniago) |
| Data da coleta | 05/08/2021 |
| Tamanho | 22.110 linhas × 10 colunas |

### Dicionário de colunas

| Coluna | Descrição | Tipo original | Tipo após tratamento |
|---|---|---|---|
| `DATE` | Data/hora da coleta do anúncio | object | removida |
| `PRICE` | Preço do imóvel (R$) | object | float |
| `ADDRESS` | Rua e bairro/setor | object | object |
| `AREAS` | Área em m² | object | float |
| `BEDROOMS` | Número de quartos | object | Int64 |
| `PARKING-SPACES` | Vagas de garagem | object | Int64 |
| `BATHROOMS` | Número de banheiros | object | Int64 |
| `CONDOMÍNIO` | Valor do condomínio (R$) | object | float |
| `IPTU` | Valor do IPTU (R$) | object | float |
| `TIPO` | Tipo do imóvel (11 categorias, ex.: `apartamentos`, `casas`, `cobertura`, `studio`, `terrenos-lotes-condominios`) | object | object |

## 3. Diagnóstico de qualidade

- **Duplicados:** nenhuma linha duplicada.
- **Tipos:** 8 das 10 colunas estavam com tipo inadequado (todas como `object`).
- **Valores ausentes** (percentual sobre 22.110 linhas):

| Coluna | Ausentes | % |
|---|---:|---:|
| `IPTU` | 15.441 | 69,84% |
| `CONDOMÍNIO` | 10.752 | 48,63% |
| `PARKING-SPACES` | 2.640 | 11,94% |
| `BEDROOMS` | 1.445 | 6,54% |
| `BATHROOMS` | 1.429 | 6,46% |
| `AREAS` | 38 | 0,17% |
| `ADDRESS` | 3 | 0,01% |

- `PRICE` não tinha `NaN`, mas continha o texto `Sob consulta` (convertido em `NaN` na limpeza).

## 4. Tratamento realizado

1. **Remoção da coluna `DATE`**, sem utilidade para a análise (todos os registros foram coletados no mesmo dia).
2. **`PRICE`, `CONDOMÍNIO`, `IPTU`:** remoção de `R$` e separador de milhar, conversão para `float`. `Sob consulta` virou `NaN`.
3. **`AREAS`:** remoção de `m²`; em intervalos (ex.: `222 - 485 m²`) foi mantido o **menor valor**.
4. **`BEDROOMS`, `PARKING-SPACES`, `BATHROOMS`:** em intervalos (ex.: `3 - 4`) foi mantido o **menor valor**; conversão para `Int64` (inteiro que aceita nulos).
5. **Valores ausentes numéricos:** preenchidos com a **mediana** da coluna, por ser mais robusta a outliers que a média (ex.: em `PRICE`, média ≈ R$ 1,23 mi vs. mediana R$ 550 mil).
6. **`ADDRESS` ausente:** as 3 linhas foram removidas.

Base final: **22.107 linhas × 9 colunas**, sem valores ausentes.

## 5. Como executar

Com os arquivos do projeto na mesma pasta:

```bash
pip install -r requirements.txt
jupyter notebook EDA_Cabral.ipynb
```

O notebook também pode ser aberto no Google Colab. Basta enviar o arquivo `2021-08-05-all.csv` para o ambiente antes de executar.

### Dependências

`pandas`, `numpy`, `openpyxl` (exportação para Excel)

## 6. Estrutura do repositório

```
├── EDA_Cabral.ipynb        # notebook com a análise
├── 2021-08-05-all.csv      # dados brutos
├── EDA - Cabral.xlsx       # base tratada (saída)
├── requirements.txt
└── README.md
```

## 7. Limitações e próximos passos

- Colunas com muitos ausentes (`IPTU`, `CONDOMÍNIO`) foram imputadas pela mediana, o que concentra os valores em um único número e reduz a variância real. Deve ser reavaliado na próxima fase.
- A coluna `PRICE` e a coluna `AREAS` possuem valores extremos (máximos de R$ 1,3 bi e 570 milhões de m²), provavelmente erros de digitação ou anúncios atípicos. A análise de outliers ainda será feita.
- Parte dos anúncios corresponde a empreendimentos com intervalo de valores; foi usado o limite inferior.
