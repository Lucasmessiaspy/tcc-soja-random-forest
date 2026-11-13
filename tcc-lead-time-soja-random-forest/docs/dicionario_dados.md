# Dicionário de dados — `dataset_modelo.parquet`

Uma linha por atracação de navio com embarque de soja em longo curso de exportação (9.925 linhas, chegadas de 2019 a 02/10/2025).

| Coluna | Papel | Origem | Descrição |
|---|---|---|---|
| `IDAtracacao` | id | Atracacao | Identificador da atracação |
| `dt_chegada` | id | Atracacao | Data/hora de chegada do navio (referência temporal) |
| `ano` | id | Data Chegada | Ano da chegada (usado para separar treino e teste) |
| `Nº do IMO` | id | Atracacao | Identificador do navio |
| `lead_time_h` | target | TemposAtracacao | T1+T2+T3+T4 = Chegada → Desatracação (horas) |
| `t1_espera_atracacao_h` | eda | TemposAtracacao | T1 – espera para atracar (não usar como feature) |
| `t2_espera_inicio_op_h` | eda | TemposAtracacao | T2 – espera para iniciar operação (não usar como feature) |
| `t3_operacao_h` | eda | TemposAtracacao | T3 – duração da operação (não usar como feature) |
| `t4_espera_desatracacao_h` | eda | TemposAtracacao | T4 – espera para desatracar (não usar como feature) |
| `mes` | feature | Data Chegada | Mês da chegada |
| `trimestre` | feature | Data Chegada | Trimestre da chegada |
| `semana_ano` | feature | Data Chegada | Semana ISO da chegada |
| `dia_semana` | feature | Data Chegada | Dia da semana (0 = segunda) |
| `sin_doy` | feature | Data Chegada | Seno do dia do ano (sazonalidade cíclica) |
| `cos_doy` | feature | Data Chegada | Cosseno do dia do ano (sazonalidade cíclica) |
| `periodo_safra` | feature | Data Chegada | pico_soja (fev–jun), safrinha_milho (jul–out), entressafra |
| `Complexo Portuário` | feature | Atracacao | Complexo portuário |
| `Porto Atracação` | feature | Atracacao | Porto/terminal (alta cardinalidade) |
| `Tipo da Autoridade Portuária` | feature | Atracacao | Porto Organizado x Terminal Autorizado |
| `Região Geográfica` | feature | Atracacao | Região do porto |
| `SGUF` | feature | Atracacao | UF do porto |
| `peso_soja_t` | feature | Carga | Toneladas de soja embarcadas na atracação |
| `peso_total_embarcado_t` | feature | Carga | Toneladas totais embarcadas na atracação |
| `pct_soja` | feature | Carga | Participação da soja no peso embarcado |
| `n_lotes` | feature | Carga | Nº de registros de carga de soja |
| `n_mercadorias` | feature | Carga | Nº de mercadorias distintas embarcadas |
| `n_destinos` | feature | Carga | Nº de destinos distintos da soja |
| `continente_destino` | feature | Carga + Instalacao_Destino | Continente com maior tonelagem |
| `flag_destino_hub` | feature | Carga + Instalacao_Destino | 1 se algum destino é Singapura (provável 'to order') |
| `fila_na_chegada` | feature | Atracacao (granel sólido) | Navios de granel sólido aguardando no complexo na chegada |
| `chegadas_7d` | feature | Atracacao (granel sólido) | Chegadas de granel sólido no complexo nos 7 dias anteriores |
| `lead_time_medio_30d` | feature | Atracacao + Tempos | Lead time médio dos navios desatracados no complexo nos 30 dias anteriores |
| `horas_chuva_7d` | feature | TemposAtracacaoParalisacao | Horas de paralisação por chuva no complexo nos 7 dias anteriores |
| `atracacoes_imo_365d` | feature | Atracacao | Chegadas do mesmo navio nos 365 dias anteriores |

**Papéis:** `target` = variável resposta; `id` = identificação/tempo (não é feature); `feature` = entra no modelo; `eda` = usada só na análise descritiva, nunca como feature (vazamento).
