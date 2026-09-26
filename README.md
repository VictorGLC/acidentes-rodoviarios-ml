# Acidentes rodoviários sob mudança temporal

Projeto de classificação da severidade de ocorrências registradas pela Polícia Rodoviária Federal. O pipeline compara uma regressão Softmax implementada em NumPy com uma implementação do scikit-learn e avalia uma MLP nos cenários histórico, fine-tuning, retreinamento completo e treinamento somente com dados recentes.

## Uso de IA generativa

A ferramenta foi utilizada para auxiliar no código, como na implementação do modelo MLP em Keras, e para esclarecer dúvidas na implementação da regressão logística. Também foi utilizada para revisão do paper.

## Requisitos

- Python 3.12
- [uv](https://docs.astral.sh/uv/) (recomendado) ou `pip`
- JupyterLab

## Dados

[Dataset no Kaggle](https://www.kaggle.com/datasets/jairsouza/acidentes-rodovias-federais): 2023 completo, 2024 completo e **somente janeiro de 2025**.

```text
data/
├── raw/
│   ├── datatran2023.csv
│   ├── datatran2024.csv
│   └── datatran2025.csv
├── d0.csv
├── d1.csv
└── d2.csv
```

O alvo é `classificacao_acidente`: classificar uma ocorrência já registrada, incluindo seu tipo e causa.

## Jupyter local

Com Python 3.12 e uv, execute na raiz do projeto:

```powershell
uv sync --locked
uv run jupyter lab
```

Execute os notebooks na ordem abaixo, sempre do início ao fim. No VS Code, selecione `.venv/Scripts/python.exe` como kernel.

Alternativa sem uv:

```powershell
python -m venv .venv
.venv/Scripts/python.exe -m pip install -r requirements.txt
.venv/Scripts/python.exe -m jupyter lab
```

## Colab

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

1. Coloque os CSVs em `MyDrive/AMMCI_PRF/data/raw/` no Google Drive.
2. Abra o notebook pelo Drive ou por upload no Colab.
3. Ajuste `PROJECT_ROOT` na primeira célula se sua pasta tiver outro nome.
4. Execute tudo na ordem e autorize a montagem do Drive.

A primeira célula concentra a configuração dos dois ambientes, a montagem do Drive e a instalação das dependências. As demais células são iguais. Não é necessário clonar o projeto ou importar módulos locais. Guarde todos os notebooks no mesmo projeto do Drive para que compartilhem dados e artefatos.

## Ordem dos notebooks

| Notebook | Responsabilidade |
|---|---|
| [01 — Auditoria e AED](notebooks/01_data_audit_eda.ipynb) | Limpeza estrutural, mapas e splits cronológicos 60/20/20 |
| [02 — Drift](notebooks/02_drift_analysis.ipynb) | Distribuições de atributos e alvo, KS e comparações categóricas |
| [03 — Softmax do zero](notebooks/03_softmax_regression.ipynb) | Gradiente em NumPy, verificação numérica e comparação com scikit-learn |
| [04 — Seleção](notebooks/04_model_selection.ipynb) | Seleção temporal da MLP em D0 |
| [05 — Experimentos temporais](notebooks/05_temporal_experiments.ipynb) | M0, MFT, MRT e MREC com avaliação final em D2 |
| [06 — Resultados](notebooks/06_results.ipynb) | Métricas, matrizes de confusão, explicabilidade e análise de erros |
| [07 — Diário de experimentos](notebooks/07_experiment_log.ipynb) | Registro das execuções relevantes |

Os notebooks aceitam como diretório atual tanto a raiz do projeto quanto a pasta `notebooks/`.

## Organização

```text
data/raw/           CSVs originais
data/               splits d0.csv, d1.csv e d2.csv
notebooks/          preparação, análises e experimentos
outputs/figures/      imagens da AED, drift, modelos e resultados
outputs/metrics/      métricas, previsões e análises
outputs/models/       configurações e pesos necessários
outputs/experiments/  diário de experimentos
```

Os mapas do notebook 01 usam `geobr` e precisam de internet no primeiro carregamento. Dados brutos, splits e pesos dos modelos ficam fora do Git.
