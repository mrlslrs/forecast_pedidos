# forecast_pedidos
Código da primeira parte para forecast de pedidos - retirei partes de eda e gráficos desnecessários ao processo, que ocupavam espaço apenas explicativo para time de negócios, e educativo para assistente e estagiário.

# Pipeline de Análise de Vendas e Clusterização de Lojas

Pipeline em Python para ETL, engenharia de features, análise de lift em eventos/campanhas e clusterização de lojas com base em dados operacionais de vendas.

## Visão geral

O script processa dados diários de pedidos por loja, cruza com um cadastro de status operacional e um calendário de datas comerciais/feriados, e gera:

- Features de série temporal (lags, médias móveis, volatilidade)
- Classificação de outliers por dia útil / fim de semana (método IQR)
- Cálculo de **lift** de pedidos em torno de datas de campanha/evento, comparado a uma baseline de dias normais
- Indicadores de momentum de curto vs. longo prazo
- Segmentação de lojas por porte, maturidade e ciclo de vida (nova / ativa / encerrada)
- Clusterização de lojas (K-Means + PCA) com base em volume, maturidade, intermitência e taxas de reserva/comparecimento
- Visualizações (heatmaps de lift por estado/evento e por dia da semana, cluster em 3D)

## Estrutura do projeto

