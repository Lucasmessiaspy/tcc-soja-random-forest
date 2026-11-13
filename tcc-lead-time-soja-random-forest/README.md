# Sazonalidade e previsão do lead time portuário das exportações de soja

Código, dados analíticos e resultados do Trabalho de Conclusão de Curso em Ciência da Computação, Centro Universitário Braz Cubas (2026).

**Título completo:** *Análise e previsão do impacto da sazonalidade no lead time portuário das exportações de soja utilizando machine learning*

**Autores:** Lucas Messias Marques, Nícolas Gabriel da Silva Josafá
**Orientadora:** Profa. Mayara dos Santos Amarante Lima

---

## Pergunta de pesquisa

Existe um padrão sazonal recorrente no tempo de estadia dos navios que embarcam soja para exportação? E um modelo Random Forest, usando apenas informações conhecidas na chegada do navio, prevê esse tempo melhor que uma regra histórica simples?

**Resposta curta (teste em 2025):** há sazonalidade clara (navios que chegam de janeiro a março têm tempo de estadia maior que os de abril a junho, em 7 de 7 anos). O Random Forest reduz o erro médio absoluto em relação ao baseline por complexo e mês, mas o ganho é modesto e o modelo explica pouca variância (R² ≈ 0,09). Os resultados indicam associação, não causalidade.

## Definições

- **Lead time portuário (alvo):** `ESTADIA` da ANTAQ, em horas, igual à soma T1 + T2 + T3 + T4:
  - T1: chegada até atracação (espera para atracar);
  - T2: atracação até início da operação;
  - T3: início até término da operação;
  - T4: término da operação até desatracação.
- **Unidade de análise:** uma atracação de navio com embarque de soja em longo curso de exportação.
- **Regra anti-vazamento:** só entra como variável explicativa o que é conhecido na chegada do navio. Os tempos T1 a T4 não entram como variáveis. A divisão entre treino e teste é temporal, e as atracações que chegaram depois de 02/10/2025 são excluídas para evitar censura.

## Dados

- **Fonte:** Estatístico Aquaviário, ANTAQ (Agência Nacional de Transportes Aquaviários), 2019 a 2025. Portal: https://aquarela.antaq.gov.br
- **Tabelas usadas por ano:** `{ano}Atracacao`, `{ano}Carga`, `{ano}TemposAtracacao`, `{ano}TemposAtracacaoParalisacao` e a dimensão `Instalacao_Destino` (incluída em `Tabelas Dimensões/`).
- **Dados brutos não estão neste repositório** (cerca de 3 GB). A base analítica já processada está em `dataset_modelo.parquet` (9.925 atracações). O dicionário de cada coluna está em [`docs/dicionario_dados.md`](docs/dicionario_dados.md).

## Estrutura

```
.
├── CreateDataset_v2.ipynb        # 1º: monta a base analítica a partir das tabelas da ANTAQ
├── Analise_Sazonalidade.ipynb    # 2º: análise descritiva e testes estatísticos da sazonalidade
├── Modelo_RandomForest.ipynb     # 3º: Random Forest, baselines, validação e importância das variáveis
├── dataset_modelo.parquet        # base analítica (saída do 1º notebook)
├── Resultados_Sazonalidade.xlsx  # saída do 2º notebook
├── Resultados_RandomForest.xlsx  # saída do 3º notebook (métricas, busca de hiperparâmetros, previsões do teste)
├── figuras/                      # gráficos usados no artigo e na apresentação
├── Tabelas Dimensões/            # dimensão de instalações de destino usada pelo 1º notebook
├── docs/
│   ├── Artigo_TCC_Sazonalidade_Soja.docx
│   ├── Apresentacao_TCC_Sazonalidade_Soja.pptx
│   ├── Resumo_ENCIBRAC_TCC.docx
│   ├── dicionario_dados.md
│   └── modelagem/TCC.brM3        # modelo entidade-relacionamento (BrModelo)
└── requirements.txt
```

## Como reproduzir

1. **Baixe os arquivos da ANTAQ** (pelo portal, em `https://download.antaq.gov.br/ea/`) para os anos de 2019 a 2025: `{ano}Atracacao.zip`, `{ano}Carga.zip`, `{ano}TemposAtracacao.zip` e `{ano}TemposAtracacaoParalisacao.zip`.
2. **Extraia** cada zip em uma pasta `Tabelas_Fato_{ano}/` na raiz do repositório, por exemplo `Tabelas_Fato_2023/2023Atracacao.txt`.
3. **Instale as dependências** em um ambiente virtual:
   ```bash
   pip install -r requirements.txt jupyter
   ```
4. **Abra o Jupyter a partir da raiz do repositório** (os caminhos são relativos a ela) e execute, nesta ordem:
   1. `CreateDataset_v2.ipynb` (gera `dataset_modelo.parquet` e `Dataset_Modelo.xlsx`);
   2. `Analise_Sazonalidade.ipynb` (gera `Resultados_Sazonalidade.xlsx` e as figuras de sazonalidade);
   3. `Modelo_RandomForest.ipynb` (gera `Resultados_RandomForest.xlsx` e as figuras do modelo).

   O 1º notebook detecta automaticamente os anos disponíveis na pasta.

**Ambiente usado no trabalho:** Python 3.12.0; versões das bibliotecas em `requirements.txt`. Semente aleatória fixada em 42.

## Principais resultados (teste em 2025, chegadas até 02/10)

| Modelo | MAE (h) | MdAE (h) | RMSE (h) | R² |
|---|---|---|---|---|
| Baseline 1: mediana global | 140,5 | 120,4 | 179,8 | −0,01 |
| Baseline 2: mediana por complexo e mês | 133,1 | 109,2 | 170,8 | 0,09 |
| **Random Forest** | **124,8** | **92,7** | 170,6 | 0,09 |

MAE = erro médio absoluto; MdAE = erro mediano absoluto. Os valores completos, o intervalo por bootstrap do baseline, a sensibilidade e a importância por bloco de variáveis estão em `Resultados_RandomForest.xlsx`.

## Limitações

- O teste cobre um único período (2025, até outubro), o que limita a generalização.
- As variáveis de carga usam valores realizados, não o plano de carga, que só é conhecido depois.
- O R² baixo indica que parte grande da variação do tempo de estadia não é explicada pelas variáveis disponíveis.
- Os resultados são associações estatísticas. Não há evidência de causalidade.

## Licença

A definir pelos autores. Os dados da ANTAQ são públicos e pertencem à agência.
