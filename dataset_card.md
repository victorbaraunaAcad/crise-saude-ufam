# Dataset Card

## A.1 Identificação
* **Nome da base:** Base Consolidada de Impactos Climáticos, Socioeconômicos e de Saúde (`base_consolidada_municipios`, `base_indicadores_uf_final` e bases temáticas regionais).
* **Grupo / integrantes:** Leonardo Brandão do Amarante, Victor Henrique Maia Baraúna, Pedro Vinicius Weil Montenegro, Gabriel Conceição dos Santos
* **Tema e pergunta motivadora:** Impacto de eventos climáticos extremos (enchentes no RS, estiagem no AM e ondas de calor no Centro-Oeste) sobre os indicadores de saúde pública (internações, custos hospitalares e mortalidade) e contexto socioeconômico (população, PIB e desemprego) no nível municipal e estadual. *Pergunta motivadora:* Como desastres ambientais extremos afetam a demanda do sistema público de saúde (SIH/SIM) e de que forma essa vulnerabilidade se relaciona com fatores socioeconômicos e demográficos nos municípios e estados atingidos?
* **Data da coleta:** Execução automatizada registrada nos logs de proveniência (`registro_proveniencia.csv`), abrangendo coletas entre 2024 e 2026.

## A.2 Fontes e proveniência
* **Fonte 1 - nome e URL:** IBGE (APIs REST de Localidades e SIDRA) — `https://servicodados.ibge.gov.br/api/v1/localidades/` e `https://sidra.ibge.gov.br/`
* **Fonte 1 - método:** Requisições HTTP GET em endpoints de API REST com extração recursiva de JSON.
* **Fonte 1 - licença / termos:** Domínio público / Dados abertos governamentais (Uso público e acadêmico).
* **Fonte 2 - nome e URL:** DATASUS (SIH - Sistema de Informações Hospitalares e SIM - Sistema de Informações sobre Mortalidade) — Ministério da Saúde do Brasil / FTP DATASUS.
* **Fonte 2 - método:** Processamento e conversão de microdados brutos em formato `.parquet` agregados no nível de município de residência e posteriormente consolidados por Unidade da Federação.
* **Fonte 2 - licença / termos:** Domínio público / Dados abertos do Sistema Único de Saúde (SUS) sob a Lei de Acesso à Informação.
* **Fonte 3 (Adicional) - nome e URL:** Portais de notícias/Defesa Civil (Sul21, IHU/Unisinos, Correio Braziliense, Diário do Nordeste, etc.) — Mapeados nas URLs de notícias sobre boletins de crise.
* **Fonte 3 - método:** Web scraping automatizado via `requests` e `BeautifulSoup` com salvamento prévio dos bytes brutos de HTML e checagem de integridade via SHA-256.
* **Fonte 3 - licença / termos:** Conteúdo jornalístico público (raspagem estritamente para fins acadêmicos e não comerciais).
* **Chave de integração:** Código IBGE do município com 7 dígitos (`codigo_ibge` para cruzamento socioeconômico), código IBGE de 6 dígitos (`codigo_ibge_6` obtido por divisão inteira por 10 para o DATASUS), sigla da Unidade da Federação (`uf` / `uf_codigo` para a tabela `base_indicadores_uf_final`) e `ano_mes` (`AAAA-MM`) para agregação temporal.

## A.3 Dicionário de variáveis

### Tabela Municipal (`base_consolidada_municipios`)
| Variável | Tipo | Descrição | Unidade |
| :--- | :--- | :--- | :--- |
| `codigo_ibge` | Numérico (`int64`) | Código identificador do município no IBGE (7 dígitos com dígito verificador). | Código IBGE |
| `nome_municipio` | Texto (`string`) | Nome oficial do município brasileiro. | N/A |
| `uf` | Texto (`string`) | Sigla da Unidade da Federação. | Sigla de UF |
| `uf_codigo` | Numérico (`float64`) | Código numérico identificador do estado no IBGE. | Código de UF |
| `codigo_ibge_6` | Numérico (`int64`) | Código IBGE truncado para 6 dígitos (utilizado no padrão DATASUS). | Código IBGE (6d) |
| `nome_norm` | Texto (`string`) | Nome do município normalizado em maiúsculas e sem acentuação/diacríticos. | N/A |
| `populacao` | Numérico (`float64`) | População residente estimada/recenseada do município (IBGE/SIDRA). | Habitantes |
| `pib_total` | Numérico (`float64`) | Produto Interno Bruto municipal a preços correntes. | Milhares de Reais (R$ 1.000,00) |
| `taxa_desocupacao` | Numérico (`float64`) | Taxa média de desocupação (desemprego) da UF (PNAD Contínua). | Porcentagem (%) |
| `total_internacoes` | Numérico (`int64`/`float64`) | Contagem total de internações hospitalares aprovadas (AIHs processadas no SIH). | Contagem de AIHs |
| `custo_total_internacoes` | Numérico (`float64`) | Valor total repassado do processamento das internações hospitalares (`VAL_TOT`). | Reais (R$) |
| `total_obitos_sim` | Numérico (`int64`/`float64`) | Total de óbitos registrados no Sistema de Informações sobre Mortalidade (SIM). | Declarações de Óbito |
| `pib_per_capita_calculado` | Numérico (`float64`) | PIB *per capita* estimado (`(pib_total * 1000) / populacao`). | R$ / habitante |
| `taxa_internacao_por_10k` | Numérico (`float64`) | Taxa de internações hospitalares por 10.000 habitantes (`(total_internacoes / populacao) * 10000`). | Internações / 10.000 hab |
| `pico_obitos_rs` / `pico_desalojados_rs` / `max_nivel_guaiba_m` | Numérico (`float64`) | Indicadores de pico e gravidade de eventos climáticos (específicos das bases por crise). | Variadas (Pessoas, Metros, etc.) |

### Tabela Estadual Agregada (`base_indicadores_uf_final`)
| Variável | Tipo | Descrição | Unidade |
| :--- | :--- | :--- | :--- |
| `uf` | Texto (`string`) | Sigla da Unidade da Federação. | Sigla de UF |
| `uf_codigo` | Numérico (`float64`) | Código numérico identificador do estado no IBGE. | Código de UF |
| `populacao_total_uf` | Numérico (`float64`) | Somatório da população estimada de todos os municípios da UF. | Habitantes |
| `pib_total_uf` | Numérico (`float64`) | Somatório do PIB de todos os municípios da UF. | Milhares de Reais |
| `taxa_desocupacao_uf` | Numérico (`float64`) | Taxa oficial de desocupação (PNAD Contínua) agregada por estado. | Porcentagem (%) |
| `total_internacoes_uf` | Numérico (`int64`) | Total consolidado de internações hospitalares do SUS na UF. | Contagem de AIHs |
| `custo_total_internacoes_uf` | Numérico (`float64`) | Custo total acumulado de internações hospitalares na UF. | Reais (R$) |
| `total_obitos_sim_uf` | Numérico (`int64`) | Total consolidado de óbitos no SIM registrados na UF. | Declarações de Óbito |
| `taxa_internacao_uf_por_10k` | Numérico (`float64`) | Taxa estadual agregada de internações por 10.000 habitantes. | Internações / 10.000 hab |

## A.4 Volume e granularidade
* **Número de linhas / colunas:**
  - **Nível Municipal (`base_consolidada_municipios.csv` / `.parquet`):** 948 linhas e 14 colunas (expandida nas bases específicas por crise).
  - **Nível Estadual (`base_indicadores_uf_final.csv` / `.parquet`):** Registros agrupados por Unidade da Federação correspondentes aos estados em análise (RS, AM, MT, MS, GO, DF).
* **O que representa uma linha:** 
  - Na base municipal: Um município brasileiro pertencente às regiões/estados cobertos pelo projeto.
  - Na base `base_indicadores_uf_final`: O consolidado macrorregional/estadual dos indicadores socioeconômicos e epidemiológicos para uma Unidade da Federação.
* **Cobertura:** Recorte territorial cobrindo os municípios e UFs das áreas diretamente afetadas pelas crises climáticas investigadas (Enchentes no RS, Estiagem no Amazonas e Ondas de Calor/Queimadas no Centro-Oeste) na janela temporal de junho de 2023 a junho de 2025 (25 meses).

## A.5 Limitações e decisões
* **Dados descartados:** Arquivos `.parquet` do DATASUS com esquemas corrompidos, colunas ausentes ou inconformidades de formatação foram ignorados no laço de leitura via tratativa de exceção (`try-except`).
* **Lacunas conhecidas:**
  - A Taxa de Desocupação (PNAD Contínua) é disponibilizada originalmente no nível de UF; na base municipal, ela é atribuída de forma homogênea a todos os municípios do estado, sendo consolidada em seu valor nativo em `base_indicadores_uf_final`.
  - Municípios sem registros no SIH ou SIM na janela temporal analisada tiveram seus totais preenchidos com zero (`0`).
  - A amostragem de dados via scraping representa recortes de pico durante as crises e não séries temporais diárias ininterruptas para todos os municípios.
* **Decisões de limpeza e agregação relevantes:**
  - **Conversão de Chaves IBGE:** Criação da chave `codigo_ibge_6` a partir de `codigo_ibge // 10` para harmonização entre a tabela territorial do IBGE e o padrão truncado do DATASUS.
  - **Agregação Estadual (`base_indicadores_uf_final`):** Agrupamento por `uf` / `uf_codigo` realizando a soma das variáveis absolutas (`populacao`, `pib_total`, `total_internacoes`, `custo_total_internacoes`, `total_obitos_sim`) e o recálculo ponderado das taxas estaduais por 10.000 habitantes.
  - **Normalização Textual:** Implementação da função `normalizar()` utilizando decomposição Unicode `NFKD`, remoção de acentos/não-ASCII e padronização em maiúsculas para joins por texto.
  - **Tratamento de Divisão por Zero:** Substituição de populações iguais a zero por `np.nan` antes do cálculo de taxas per capita, evitando valores infinitos (`inf`).
  - **Ajuste Monetário do PIB:** Multiplicação da variável `pib_total` por 1.000 para converter a escala original do IBGE (milhares de Reais) para Reais nominais antes do cálculo per capita.

## A.6 Considerações éticas
* **Contém dados pessoais?:** Não. Os dados de saúde do SIH e SIM foram anonimizados na origem pelo DATASUS e sumarizados/agregados por código de município de residência e UF, impedindo a reidentificação de pacientes ou indivíduos.
* **Restrições de uso / redistribuição:** Uso estritamente acadêmico, educacional e científico, respeitando os termos de reutilização das bases abertas governamentais e as políticas de direito autoral das fontes de notícias.
* **robots.txt verificado?:** Sim. A conformidade ética de scraping foi tratada mediante verificação prévia do `robots.txt` de cada domínio via `urllib.robotparser`, com identificação explícita de `User-Agent` com contato acadêmico e imposição de um atraso mínimo (*delay*) de 2 segundos entre requisições para evitar sobrecarga aos servidores.
