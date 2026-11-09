# Inadimplência de crédito e indicadores macroeconômicos no Brasil

**Trabalho de Conclusão de Curso · MBA em Data Science e Analytics · USP**

Como juros, inflação, desemprego e atividade econômica se relacionam com a inadimplência de crédito ao longo do tempo? Este projeto reúne os dados e os notebooks usados para explorar essa pergunta em um painel mensal de abril de 2012 a agosto de 2025.

## Dados disponíveis

Os dois arquivos cobrem 161 meses. Cada observação combina o mês de referência (`mes_ano`) com um segmento de crédito (`porte`). A variável-alvo `default_percent` é calculada na consolidação como o percentual de ativos problemáticos sobre a carteira ativa.

| Arquivo | Observações | Recorte |
| --- | ---: | --- |
| [Painel em CSV](BASE_DADOS/data/dataset_macro_painel_limpo.csv) | 2.175 | 14 segmentos de pessoas físicas e jurídicas, incluindo categorias com classificação indisponível. |
| [Painel em Parquet](BASE_DADOS/data/dataset_macro_painel_limpo_com_dummies.parquet) | 1.288 | Oito segmentos de pessoas físicas com classificação disponível. Cada segmento tem uma observação por mês. É a base usada no notebook econométrico. |

Entre os indicadores do painel estão Selic, IPCA, desemprego, IBC-Br e câmbio. O notebook de consolidação consulta séries do Sistema Gerenciador de Séries Temporais do Banco Central do Brasil. [Documentação do SGS](https://www.bcb.gov.br/gestao_site/webservices.asp?frame=1)

## Notebooks

| Etapa | Notebook | Conteúdo |
| --- | --- | --- |
| Consolidação | [`consilide_data.ipynb`](BASE_DADOS/code/consilide_data.ipynb) | Agregação dos dados de crédito por mês e segmento, cálculo do indicador de inadimplência e integração com séries macroeconômicas. |
| Tratamento | [`tratar_dados.ipynb`](BASE_DADOS/code/tratar_dados.ipynb) | Preparação do painel, seleção do recorte de pessoas físicas para o Parquet e testes de estacionariedade. |
| Análise preliminar | [`resultado_preliminar.ipynb`](BASE_DADOS/code/resultado_preliminar.ipynb) | Correlações de Pearson com a variável-alvo, triagem de variáveis correlacionadas entre si e exploração de defasagens. |
| Estrutura do painel | [`modelo_econometrico.ipynb`](BASE_DADOS/code/modelo_econometrico.ipynb) | Leitura do Parquet, indexação por segmento e mês e verificação do balanceamento do painel. |

Os arquivos de [correlações sem defasagem](BASE_DADOS/code/correlacao_direta_alvo_sem_lags.csv) e [variáveis contemporâneas selecionadas](BASE_DADOS/code/variaveis_contemporaneas_selecionadas.csv) registram etapas da análise exploratória.

## Como começar

Na raiz do repositório, instale `pandas` e `pyarrow` com `python -m pip install pandas pyarrow`. O exemplo abaixo carrega o recorte de pessoas físicas e mostra a quantidade de observações e a média do indicador por segmento.

```python
import pandas as pd

painel = pd.read_parquet("BASE_DADOS/data/dataset_macro_painel_limpo_com_dummies.parquet")
resumo = painel.groupby("porte")["default_percent"].agg(
    observacoes="size",
    media_percentual="mean",
)
print(resumo.round(2))
```

Para explorar o recorte mais amplo, leia o CSV com `sep=";"`, `decimal=","` e `parse_dates=["mes_ano"]`. Para executar o notebook econométrico, use `BASE_DADOS/code` como diretório de trabalho. A reprodução integral das etapas de consolidação e tratamento exige os arquivos brutos e intermediários referenciados nos notebooks e a adaptação dos caminhos locais.
