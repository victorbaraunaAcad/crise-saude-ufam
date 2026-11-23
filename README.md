# Projeto Crise e Saúde (IComp/UFAM)

Como a falta (seca no AM) e o excesso de água (enchente no RS) afetam as internações por doenças ligadas à água, município a município. Os dados do Centro-Oeste (calor) ficam no repositório como registro da fase 1, mas não entram na análise.

## Estrutura

| Pasta / arquivo | O que tem |
|---|---|
| `relatorio.md` | Big Idea, ABT, storyboard do vídeo e contribuições individuais |
| `requirements.txt` | Dependências Python |
| `notebooks/00_coleta_ibge.ipynb` | Coleta IBGE (antigo `coleta.ipynb`) |
| `notebooks/00_coleta_datasus.ipynb` | Coleta SIH/SIM/SINAN (antigo `coleta_DATASUS.ipynb`) |
| `notebooks/01_eda.ipynb` | EDA e ampliação da base (a preencher) |
| `notebooks/02_modelagem.ipynb` | Modelos supervisionados e agrupamento (a preencher) |
| `notebooks/legado_fase1/` | Notebooks da entrega da fase 1, preservados |
| `data/raw/` | Dados brutos, nunca editados (parquet via Git LFS, JSON de APIs, conferência manual) |
| `data/processed/` | Bases tratadas e consolidadas (`.csv` e `.parquet`) |
| `figures/` | Gráficos usados no video |
| `docs/dataset_card.md` | Dataset card |
| `docs/proveniencia.csv`, `docs/registro_proveniencia.csv` | Logs de proveniência |
| `docs/legado/` | Entrega e README da fase 1 |

## Observações

* Os `.parquet` usam **Git LFS**: rode `git lfs install` antes de clonar (ou `git lfs pull` depois), senão vêm como arquivos de texto.
* Os notebooks da coleta rodam no Colab: clonam o repo e fazem `%cd` para a **raiz**, de onde resolvem `data/raw`, `data/processed` e `docs/`. Localmente, execute-os com a raiz como diretório de trabalho.
* Novos arquivos de coleta vão direto em `data/raw/` (ex.: `RAIZ_BRUTOS = RAIZ / "data" / "raw"`).
