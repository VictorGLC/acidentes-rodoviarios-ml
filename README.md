# Acidentes rodoviários — trabalho de IA

Trabalho de graduação sobre aprendizagem de máquina e mudança temporal nos acidentes da PRF. O plano é implementar Softmax em NumPy, comparar com scikit-learn e usar uma MLP nos experimentos temporais.

**Primeira entrega:** auditoria e análise exploratória no [notebook 01](notebooks/01_data_audit_eda.ipynb). A preparação fica no próprio notebook; arquivos `.py` serão usados principalmente para os modelos implementados à mão.

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

Os CSVs não são versionados. O alvo é `classificacao_acidente`: classificar uma ocorrência já registrada, incluindo seu tipo e causa.

## Jupyter local

Com Python 3.12 e uv, execute na raiz do projeto:

```powershell
uv sync --locked
uv run jupyter lab
```

Abra `notebooks/01_data_audit_eda.ipynb` e execute as células na ordem. No VS Code, selecione `.venv/Scripts/python.exe` como kernel.

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

A primeira célula concentra a configuração dos dois ambientes. No Colab, instala o geobr e monta o Drive; as demais células são iguais. Não é necessário clonar o projeto ou importar módulos locais.

## Organização

```text
data/raw/           CSVs originais
data/               splits d0.csv, d1.csv e d2.csv
docs/               especificação e planejamento
notebooks/          preparação, análises e experimentos
outputs/figures/    imagens da AED e mapas
```

As tabelas de auditoria aparecem no notebook. Os conjuntos `d0`, `d1` e `d2` são salvos em `data/d0.csv`, `data/d1.csv` e `data/d2.csv`. As figuras ficam em `outputs/figures/`. Não há Parquets ou arquivos de métricas. Os mapas usam geobr e precisam de internet no primeiro carregamento.

Para carregar um split nos próximos notebooks, use UTF-8, separador `;`, BR como categoria e parsing do timestamp:

```python
d0 = pd.read_csv(DATA_DIR / "d0.csv", sep=";", encoding="utf-8",
                 dtype={"br": "string"}, parse_dates=["timestamp"])
```

Os CSVs preservam colunas de auditoria e mapas; para modelagem, selecionar apenas as features previstas, excluindo identificadores e informações de vítimas.

Os notebooks 01 (auditoria/AED) e [02 (análise de drift)](notebooks/02_drift_analysis.ipynb) estão implementados. Os notebooks 03–06 estão reservados para Softmax, seleção de modelos, experimentos temporais e resultados.

Execute o notebook 02 após gerar os três splits no notebook 01. Ele lê os CSVs existentes e compara as distribuições com ECDF, KS, proporções e qui-quadrado quando aplicável. As tabelas ficam no notebook e as novas figuras usam o prefixo `drift_`. Nenhum modelo é treinado ou atributo alterado nessa etapa.

## Decisões da auditoria

- 146.400 registros originais; exclusão explícita dos três sem alvo.
- Fernando de Noronha mantido, apesar de ficar fora da checagem continental aproximada.
- D0/D1/D2 em ordem cronológica, com proporções 60/20/20 e empates nas fronteiras exibidos.
- Nenhum ajuste de scaler, encoder ou modelo. D2 fica fora das decisões de modelagem.
- Categorias preservadas; Top-N e “Outros” são usados apenas nos gráficos.

Codex auxiliou na implementação e verificação. O aluno deve revisar as decisões e declarar o uso de IA no artigo, conforme a especificação.
