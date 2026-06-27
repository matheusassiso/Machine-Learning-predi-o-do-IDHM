# Previsão do IDHM com Machine Learning — Random Forest em R

**Trabalho de Conclusão de Curso | UFMS · Ciências Econômicas · 2023**

![R](https://img.shields.io/badge/R-276DC3?style=flat&logo=r&logoColor=white)
![RStudio](https://img.shields.io/badge/RStudio-75AADB?style=flat&logo=rstudio&logoColor=white)
![Status](https://img.shields.io/badge/status-concluído-brightgreen?style=flat)
![Licença](https://img.shields.io/badge/licença-MIT-blue?style=flat)

> Aplicação do algoritmo **Random Forest** para regressão na previsão do **Índice de Desenvolvimento Humano Municipal (IDHM)** dos estados brasileiros no período de 2012 a 2021, utilizando dados socioeconômicos do Atlas Brasil/IBGE.

---

## Sobre o Projeto

Este repositório contém o código, os dados e a documentação do Trabalho de Conclusão de Curso *"Aplicação do Machine Learning na Previsão de Índices Econômicos: o IDHM com o Modelo Random Forest de 2012 à 2021"*, defendido no Curso de Ciências Econômicas da Universidade Federal de Mato Grosso do Sul (UFMS) em 2023.

O objetivo central é demonstrar como técnicas de **Machine Learning** podem ser aplicadas à análise de índices econômicos consolidados, servindo como referência prática e introdutória para economistas, pesquisadores e cientistas de dados que desejam incorporar aprendizado de máquina às suas análises.

---

## Contexto e Motivação

O IDHM é uma adaptação brasileira do IDH (Índice de Desenvolvimento Humano), publicado pelo PNUD, calculado a partir de três dimensões:

| Dimensão | Indicadores utilizados |
|---|---|
| **Educação** | Escolaridade da população adulta e fluxo escolar jovem |
| **Longevidade** | Esperança de vida ao nascer e mortalidade infantil |
| **Renda** | Renda *per capita* e percentual de pobres |

A aplicação do Random Forest ao IDHM busca identificar quais indicadores socioeconômicos mais influenciam o índice e avaliar a capacidade preditiva do modelo — uma ponte entre a econometria tradicional e as técnicas modernas de ciência de dados.

---

## Metodologia

```
Coleta de Dados → Engenharia de Dados → Análise Exploratória → Modelagem → Avaliação
```

### 1. Coleta de Dados
Dados de 27 unidades federativas (26 estados + Distrito Federal) + Brasil, de 2012 a 2021 (280 observações), extraídos do **Atlas Brasil** (IBGE e Registros Administrativos).

**Variáveis coletadas:**
- IDHM (variável-alvo) e seus subíndices (educação, longevidade, renda)
- Renda *per capita*, percentual de pobres, população total
- Esperança de vida, mortalidade infantil
- Média de anos de estudo, taxas de analfabetismo (15, 18 e 25 anos)
- Índice de Gini e índice de Theil L

### 2. Engenharia de Dados
- Importação de múltiplos arquivos `.xlsx` com `readxl`
- Reestruturação para formato *tidy* (pivotamento de colunas de ano) com `tidyr`
- Junção dos *data frames* por ano e territorialidade com `plyr`
- Verificação e tratamento de valores ausentes (`NA`)

### 3. Análise Exploratória de Dados (AED)
- Matriz de correlação entre todos os indicadores (`corrplot`)
- *Scatterplot matrix* das variáveis com o IDHM
- Regressões lineares simples univariadas por indicador (`ggplot2`, `ggpubr`)
- Análise de quantis e identificação de *outliers*
- Remoção de variáveis com baixo poder explicativo (Gini, Theil L e população total — R² < 0,07)

### 4. Desenvolvimento e Treinamento do Modelo
- Partição dos dados: **80% treino / 20% teste** (`caTools`, `caret`)
- **Modelo 1:** Random Forest com 500 árvores e `mtry = 3`
- Otimização do hiperparâmetro `mtry` via `tuneRF` → valor ótimo confirmado em 3
- **Modelo 2:** Random Forest com 500 árvores e `mtry = 4` (comparativo)

### 5. Avaliação do Modelo
- Métricas de desempenho: % de variância explicada, MSE e RMSE
- Importância de variáveis: %IncMSE e IncNodePurity
- Comparação entre valores reais e preditos
- Teste adicional com partição 90/10 para validação de robustez

---

## Resultados

### Desempenho dos Modelos

| Modelo | `ntree` | `mtry` | % Variância Explicada | RMSE |
|---|:---:|:---:|:---:|:---:|
| **Modelo 1** | 500 | 3 | **97,60%** | 0,00842 |
| **Modelo 2** | 500 | 4 | **97,71%** | 0,00817 |
| Modelo 90/10 (ref.) | 500 | 3 | 97,52% | 0,00601 |

> O IDHM varia de 0 a 1 — um RMSE na ordem de 0,008 representa erro médio inferior a 1 ponto percentual, demonstrando excelente capacidade preditiva do modelo.

### Importância das Variáveis (Modelo 1 — %IncMSE)

| Rank | Variável | %IncMSE |
|:---:|---|:---:|
| 1 | Renda *per capita* | 23,03 |
| 2 | Subíndice de frequência de escolaridade | 21,78 |
| 3 | Subíndice de escolaridade da população | 20,12 |
| 4 | Esperança de vida | 19,62 |
| 5 | Média de anos de estudo | 19,72 |

A variável `ano` apresentou a menor importância relativa (7,87), indicando que as dimensões socioeconômicas transversais possuem maior poder preditivo sobre o IDHM do que a tendência temporal isolada.

---

## Estrutura do Repositório

```
.
├── dados/                              # Datasets socioeconômicos (.xlsx)
│   ├── IDHM.xlsx                       # Variável-alvo
│   ├── renda.per.capita.xlsx
│   ├── IDHM.educação.xlsx
│   ├── IDHM.longevidade.xlsx
│   ├── IDHM.renda.xlsx
│   ├── esperança.de.vida.xlsx
│   ├── mortalidade_infantil.xlsx
│   ├── media_anos_de_estudo.xlsx
│   ├── sub.esco.pop.xlsx
│   ├── sub.freq.esco.xlsx
│   ├── porcent_pobres.xlsx
│   ├── população_total.xlsx
│   ├── analfabetismo_15_anos.xlsx
│   ├── analfabetismo_18_anos.xlsx
│   ├── analfabetismo_25_anos.xlsx
│   ├── indice_gini.xlsx
│   └── ind_theil_L.xlsx
│
├── scripts/                            # Scripts R por etapa do pipeline
│   ├── 01_engenharia_dados.R           # Importação, limpeza e join
│   ├── 02_analise_exploratoria.R       # AED, correlações e visualizações
│   └── 03_modelo_random_forest.R       # Treinamento, tuning e avaliação
│
├── outputs/                            # Visualizações geradas
│   ├── matriz_correlacao_IDHM.png
│   ├── relacao_IDHM_renda_per_capita.png
│   └── variacao_predicao_IDHM.png
│
├── notebooks/                          # R Markdown — análise completa e reproduzível
│   └── ML_regressao_IDHM.Rmd
│
├── predicting IDHM.Rproj              # Projeto RStudio
└── README.md
```

> **Obs.:** Os arquivos encontram-se atualmente na raiz do repositório. A estrutura acima representa a organização recomendada para reprodução do pipeline completo.

---

## Como Reproduzir

### Pré-requisitos

- R (>= 4.0)
- RStudio (recomendado)

### Instalação dos pacotes

```r
install.packages(c(
  "tidyr",
  "readxl",
  "plyr",
  "corrplot",
  "RColorBrewer",
  "ggplot2",
  "ggpubr",
  "caret",
  "randomForest",
  "caTools"
), dependencies = TRUE)
```

### Execução

```
1. Clone o repositório
2. Abra `predicting IDHM.Rproj` no RStudio
3. Execute os scripts na sequência: 01 → 02 → 03
   ou acesse o notebook R Markdown para a análise completa e documentada
```

---

## Fontes de Dados

| Fonte | Dados | Acesso |
|---|---|---|
| Atlas Brasil (IBGE) | IDHM e indicadores socioeconômicos (2012–2021) | [atlasbrasil.org.br](http://www.atlasbrasil.org.br/acervo/atlas) |
| IBGE / Registros Administrativos | Mortalidade, analfabetismo, escolaridade | Via Atlas Brasil |
| PNUD Brasil | Metodologia IDHM | [pnud.org.br](https://www.br.undp.org) |

---

## Tecnologias

| Categoria | Tecnologia |
|---|---|
| Linguagem | R |
| IDE | RStudio |
| Modelagem | `randomForest`, `caret` |
| Manipulação de dados | `tidyr`, `readxl`, `plyr` |
| Visualização | `ggplot2`, `ggpubr`, `corrplot`, `RColorBrewer` |
| Particionamento | `caTools` |

---

## Referência Acadêmica

```
OLIVEIRA, Matheus Assis de. Aplicação do Machine Learning na Previsão de Índices
Econômicos: O IDHM com o Modelo Random Forest de 2012 à 2021. Trabalho de
Conclusão de Curso (Bacharelado em Ciências Econômicas) — Escola de Administração
e Negócios, Universidade Federal de Mato Grosso do Sul, Campo Grande, 2023.
```

---

## Contato

**Matheus Assis de Oliveira**
Bacharel em Ciências Econômicas — UFMS

[![Email](https://img.shields.io/badge/Email-matheusassiso687%40gmail.com-red?style=flat&logo=gmail&logoColor=white)](mailto:matheusassiso687@gmail.com)
[![GitHub](https://img.shields.io/badge/GitHub-matheusassiso-181717?style=flat&logo=github&logoColor=white)](https://github.com/matheusassiso)
