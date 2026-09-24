# Projeto de Forecasting de Demanda no Varejo

## Visão Geral

Este repositório contém as **Partes 1 e 2 de um projeto end-to-end de forecasting dividido em quatro etapas**, com foco em análise de demanda no varejo.

O estágio atual constrói a base analítica necessária antes do treinamento dos modelos de forecasting. Ele contempla ingestão de dados, limpeza, controles de qualidade, engenharia de atributos temporais e comportamentais, análise de impacto de eventos, tratamento de outliers, diagnósticos operacionais e segmentação de lojas.

O objetivo é transformar dados operacionais brutos em uma base estruturada e pronta para modelagem, capaz de representar não apenas o histórico de demanda, mas também sazonalidade, eventos comerciais, maturidade das lojas, consistência operacional e comportamento de curto e longo prazo.

> **Status atual do projeto:** Partes **1/4** e **2/4** concluídas.  
> Treinamento dos modelos de forecasting, backtesting, deploy e monitoramento estão reservados para as Partes 3 e 4.

---

## Roadmap do Projeto

O projeto completo de forecasting está organizado em quatro etapas:

| Parte | Etapa | Status |
|---|---|---|
| **Parte 1** | Engenharia de Dados, Limpeza e Qualidade | ✅ Concluída |
| **Parte 2** | Engenharia de Features, Inteligência de Demanda e Segmentação | ✅ Concluída |
| **Parte 3** | Modelagem de Forecasting, Seleção de Modelos e Backtesting | 🔜 Planejada |
| **Parte 4** | Deploy, Monitoramento e Entrega Analítica | 🔜 Planejada |

### Parte 1 — Engenharia de Dados e Qualidade

A Parte 1 constrói a base analítica utilizada ao longo de todo o projeto.

Ela inclui:

- ingestão de dados transacionais e operacionais;
- padronização de schema e tipos de dados;
- filtragem por loja e aplicação de regras de negócio;
- integração de metadados operacionais;
- detecção de registros duplicados;
- diagnóstico de valores ausentes;
- análise de cobertura temporal;
- normalização de campos geográficos e categóricos;
- identificação de outliers com regras baseadas em IQR;
- separação entre observações válidas e registros que exigem tratamento diagnóstico.

O resultado é uma base diária de demanda por loja, limpa e estruturada.

### Parte 2 — Engenharia de Features e Inteligência de Demanda

A Parte 2 transforma a base tratada em uma camada analítica mais rica para forecasting e segmentação.

Ela inclui:

- features de lag;
- médias móveis;
- volatilidade móvel;
- features de calendário;
- comportamento em dias úteis e finais de semana;
- janelas de eventos e campanhas;
- distância temporal até e desde eventos comerciais;
- momentum de curto e longo prazo;
- coeficiente de variação;
- classificação do ciclo de vida das lojas;
- segmentação de porte com base em demanda;
- indicadores de reserva e comparecimento;
- features de inatividade operacional;
- estimativa de lift por evento;
- cálculo de baseline para campanhas;
- análise de participação da demanda por dia da semana;
- clusterização de lojas com K-Means;
- análise de silhouette;
- redução de dimensionalidade com PCA e visualização 3D dos clusters.

Essas variáveis são preparadas para se tornarem candidatas a preditores e controles analíticos nos modelos de forecasting da Parte 3.

---

## Arquitetura do Pipeline

```text
Dados Operacionais Brutos
        │
        ▼
Ingestão de Dados
        │
        ▼
Padronização de Schema
        │
        ▼
Integração de Metadados Operacionais
        │
        ▼
Limpeza e Regras de Negócio
        │
        ▼
Enriquecimento com Calendário e Eventos Comerciais
        │
        ▼
Detecção de Outliers
        │
        ▼
Engenharia de Features Temporais
        │
        ├── Lags
        ├── Médias Móveis
        ├── Desvios-Padrão Móveis
        ├── Momentum
        └── Variabilidade
        │
        ▼
Features de Comportamento Operacional
        │
        ├── Comparecimento
        ├── Performance de Reservas
        ├── Ciclo de Vida da Loja
        └── Indicadores de Inatividade
        │
        ▼
Análise de Lift por Evento
        │
        ▼
Segmentação de Lojas
        │
        ├── Robust Scaling
        ├── K-Means
        ├── Silhouette Analysis
        └── Visualização PCA 3D
        │
        ▼
Base Analítica Pronta para Modelagem
        │
        ▼
Parte 3 — Modelagem de Forecasting
```

---

## Principais Componentes Analíticos

### 1. Enriquecimento de Calendário e Sazonalidade

O pipeline cria features temporais capazes de representar padrões recorrentes de demanda.

Entre elas:

- ano;
- mês;
- trimestre;
- semana ISO;
- dia da semana;
- dia útil versus final de semana;
- eventos comerciais;
- janelas de campanha;
- dias até o próximo evento;
- dias desde o último evento.

Períodos comerciais passam a ser representados como variáveis analíticas, e não apenas como rótulos descritivos.

---

### 2. Detecção de Outliers

Os outliers de demanda são identificados com base no **Intervalo Interquartil (IQR)**.

O cálculo é realizado separadamente para diferentes perfis de dia operacional, reduzindo o risco de classificar como anomalia um comportamento esperado de dias úteis ou finais de semana.

```text
IQR = Q3 - Q1
Limite Inferior = Q1 - 1,5 × IQR
Limite Superior = Q3 + 1,5 × IQR
```

Os outliers identificados são mantidos separadamente para fins de diagnóstico, enquanto a base analítica principal pode excluí-los das etapas seguintes de modelagem.

---

### 3. Features de Lag

O histórico de demanda é representado por múltiplos horizontes de lag.

```text
lag_1
lag_7
lag_14
lag_21
lag_28
```

Essas variáveis fornecem à futura camada de forecasting informações sobre:

- comportamento imediatamente anterior;
- recorrência semanal;
- comportamento de médio prazo;
- mudanças em relação a períodos anteriores.

---

### 4. Features de Média Móvel

São calculadas médias móveis em diferentes janelas históricas.

```text
Média móvel de 7 dias
Média móvel de 14 dias
Média móvel de 21 dias
Média móvel de 28 dias
```

Os cálculos são deslocados no tempo para evitar o uso da observação atual na construção das features históricas.

Isso ajuda a reduzir o risco de **target leakage** nas futuras etapas de forecasting.

---

### 5. Volatilidade da Demanda

Desvios-padrão móveis são calculados para medir a instabilidade da demanda em diferentes horizontes.

Essas variáveis ajudam a diferenciar lojas com:

- demanda estável;
- alta volatilidade;
- picos temporários;
- comportamento operacional estruturalmente irregular.

Também é calculado um coeficiente de variação para medir volatilidade de forma proporcional ao nível de demanda.

---

### 6. Momentum da Demanda

O pipeline compara médias móveis de curto prazo com médias de horizontes mais longos.

```text
Média de 7 dias / Média de 21 dias
Média de 7 dias / Média de 28 dias
```

A partir disso, o comportamento da demanda pode ser classificado em padrões como:

- crescimento;
- queda;
- estabilidade;
- novo crescimento;
- ausência de movimento.

Isso fornece uma representação interpretável da direção recente da demanda.

---

## Análise de Lift de Eventos e Campanhas

Um dos principais componentes da Parte 2 é a estimativa do impacto incremental associado a eventos comerciais.

O pipeline cria baselines históricos e compara a demanda observada durante os eventos com o comportamento esperado em condições normais.

```text
Lift (%) =
(Demanda Observada - Demanda Baseline)
--------------------------------------
           Demanda Baseline
                  × 100
```

### Estratégia de Baseline Dinâmico

Uma janela fixa pode não conter histórico suficiente em todos os casos.

Por isso, o pipeline utiliza múltiplas janelas de fallback.

```text
14 dias
21 dias
28 dias
42 dias
56 dias
90 dias
180 dias
365 dias
```

A primeira janela histórica com quantidade suficiente de observações válidas é utilizada para estimar o baseline do evento.

Essa abordagem torna o cálculo de lift mais robusto para lojas com cobertura histórica limitada.

---

## Classificação de Zeros Operacionais

Uma observação com demanda zero nem sempre representa a mesma condição operacional.

O pipeline diferencia diferentes situações.

### Operação normal

Existem pedidos registrados.

### Zero operacional

Não houve pedidos, embora houvesse capacidade operacional disponível.

### Zero real

Não houve demanda nem atividade operacional observada.

Essa distinção evita que anomalias operacionais sejam interpretadas automaticamente como comportamento real de demanda.

---

## Ciclo de Vida das Lojas

As lojas são classificadas de acordo com sua cobertura histórica disponível.

```text
nova
ativa
encerrada
```

A classificação considera a primeira e a última data observada em relação à data mais recente da base.

Essa feature poderá ajudar os modelos de forecasting a diferenciar operações maduras de unidades recém-abertas ou descontinuadas.

---

## Proxy de Porte da Loja

O porte é estimado utilizando a demanda histórica mediana.

As lojas são divididas em três grupos:

```text
pequena
média
grande
```

Isso fornece um proxy operacional quando não existe uma medida formal de tamanho físico ou comercial.

---

## Estratégia de Tratamento de Valores Ausentes

Features de séries temporais naturalmente geram valores ausentes, principalmente no início do histórico de cada loja.

Em vez de aplicar diretamente uma estatística global, o pipeline utiliza uma estratégia hierárquica.

### Nível 1

Mediana móvel dentro da própria loja.

### Nível 2

Mediana móvel dentro da respectiva rede ou grupo de varejo.

### Nível 3

Fallback para a mediana global.

Essa abordagem busca preservar as características locais da demanda sempre que possível.

---

## Clusterização de Lojas

A Parte 2 também inclui uma camada de segmentação não supervisionada.

As lojas são agrupadas de acordo com características operacionais e de demanda, como:

```text
Demanda móvel de 28 dias
Idade da loja
Inatividade operacional
Taxa de reserva
Taxa de comparecimento
```

### Pré-processamento

As features são normalizadas utilizando:

```python
RobustScaler()
```

O `RobustScaler` é utilizado por ser menos sensível a valores extremos do que técnicas convencionais de padronização.

---

## K-Means e Silhouette Analysis

O K-Means é utilizado para identificar grupos de lojas com perfis operacionais semelhantes.

O pipeline avalia diferentes valores de `k` por meio do **silhouette score**.

Essa análise fornece uma referência quantitativa para o número de clusters suportado pela estrutura dos dados.

Os perfis dos clusters são posteriormente resumidos com base nas médias das features utilizadas.

---

## Visualização PCA 3D

A Análise de Componentes Principais (PCA) é utilizada para projetar os perfis multidimensionais das lojas em três dimensões.

A visualização interativa permite explorar:

- separação entre clusters;
- posicionamento relativo das lojas;
- volume de demanda;
- maturidade operacional;
- intermitência.

A visualização em Plotly também pode ser exportada como arquivo HTML interativo.

---

## Outputs Analíticos de Negócio

A implementação atual gera visões analíticas como:

### Performance de eventos por rede

Analisa demanda média, baseline, lift e volume de observações.

### Performance de eventos por região geográfica

Avalia como eventos comerciais se comportam em diferentes mercados.

### Distribuição da demanda por dia da semana

Mensura a contribuição de cada dia para o volume total.

### Heatmaps por segmento

Visualizam lift de eventos e comportamento semanal por perfil operacional.

### Perfis de cluster

Resumem grupos de lojas com características semelhantes de demanda e operação.

---

## Controles de Qualidade dos Dados

Antes da etapa de forecasting, o pipeline executa diferentes verificações diagnósticas.

Entre elas:

- registros duplicados;
- percentual de valores ausentes por feature;
- cobertura temporal por loja;
- quantidade de outliers removidos;
- disponibilidade histórica;
- detecção de baselines inválidos ou ausentes.

Esses controles são importantes porque a performance de forecasting depende diretamente da qualidade e continuidade das séries históricas utilizadas.

---

## Stack Tecnológica

As etapas atuais utilizam:

- **Python**
- **Pandas**
- **NumPy**
- **Matplotlib**
- **Seaborn**
- **Plotly**
- **scikit-learn**

Principais componentes estatísticos e de Machine Learning:

- `RobustScaler`
- `KMeans`
- `silhouette_score`
- `PCA`

---

## Estrutura do Repositório

Uma possível estrutura para o projeto é:

```text
retail-demand-forecasting/
│
├── README.md
├── requirements.txt
│
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
│
├── notebooks/
│   ├── 01_data_preparation.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_forecasting.ipynb
│   └── 04_model_evaluation.ipynb
│
├── src/
│   ├── data/
│   ├── features/
│   ├── analysis/
│   ├── models/
│   └── visualization/
│
├── outputs/
│   ├── figures/
│   ├── clusters/
│   └── forecasts/
│
└── models/
```

A implementação atual corresponde principalmente às responsabilidades esperadas em:

```text
src/data/
src/features/
src/analysis/
src/visualization/
```

---

# Parte 3 — Modelagem de Forecasting

A Parte 3 introduzirá a camada preditiva do projeto.

Entre as atividades planejadas estão:

- definição dos horizontes de previsão;
- estratégia de treino, validação e teste para séries temporais;
- rolling-origin backtesting;
- modelos baseline;
- seleção de features;
- comparação entre modelos;
- otimização de hiperparâmetros;
- análise de erros;
- avaliação de performance por segmento.

As famílias de modelos poderão incluir abordagens estatísticas, Machine Learning e algoritmos de gradient boosting, dependendo das características finais da base.

A escolha dos modelos não é definida antecipadamente nas Partes 1 e 2 porque deverá ser orientada pelos resultados de backtesting, e não por uma decisão arbitrária de arquitetura.

---

# Parte 4 — Deploy, Monitoramento e Entrega

A etapa final transformará a abordagem de forecasting selecionada em um fluxo orientado à produção.

Entre os componentes planejados estão:

- pipeline de geração de previsões;
- persistência dos modelos;
- inferência agendada;
- monitoramento das previsões;
- monitoramento de erro;
- controles de data drift;
- estratégia de retreinamento;
- outputs direcionados ao negócio;
- integração com dashboards;
- arquitetura de deploy.

Essa etapa concluirá a transição entre análise exploratória e uma solução operacional de forecasting.

---

## Filosofia de Modelagem

> **Um projeto de forecasting não deve começar pela escolha do modelo. Deve começar pelo entendimento do processo que gera os dados.**

Por isso, as Partes 1 e 2 concentram grande parte do esforço em:

- consistência histórica;
- sazonalidade;
- comportamento operacional;
- efeitos de eventos comerciais;
- volatilidade da demanda;
- maturidade das lojas;
- observações anômalas;
- segmentação.

O objetivo é fornecer à Parte 3 uma base analítica confiável, em vez de esperar que um algoritmo de forecasting compense problemas não resolvidos de qualidade de dados e contexto de negócio.

---

## Status Atual

```text
[██████████----------] 50%
```

### Concluído

- [x] Ingestão de dados
- [x] Limpeza de dados
- [x] Diagnósticos de qualidade
- [x] Enriquecimento de calendário
- [x] Features de eventos comerciais
- [x] Detecção de outliers por IQR
- [x] Features de lag
- [x] Estatísticas móveis
- [x] Momentum de demanda
- [x] Features operacionais
- [x] Ciclo de vida das lojas
- [x] Análise de lift
- [x] Segmentação de lojas
- [x] Visualização com PCA

### Próximas Etapas

- [ ] Modelos baseline de forecasting
- [ ] Validação temporal
- [ ] Framework de backtesting
- [ ] Seleção de features
- [ ] Comparação entre modelos
- [ ] Otimização de hiperparâmetros
- [ ] Diagnóstico de erros
- [ ] Deploy do modelo
- [ ] Monitoramento
- [ ] Dashboard / entrega para negócio

---

## Aviso

Este repositório representa uma **arquitetura de forecasting orientada a portfólio**, construída com estruturas de negócio anonimizadas e generalizadas.

A implementação atual deve ser entendida como a **camada de preparação de dados e inteligência analítica do sistema de forecasting**, e não como o modelo preditivo final.

As Partes 3 e 4 ampliarão essa base com desenvolvimento dos modelos, validação, deploy e monitoramento.
