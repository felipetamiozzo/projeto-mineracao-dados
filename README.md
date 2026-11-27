# 🎓 Mineração de Dados — Análise de Aprovação e Reprovação de Estudantes

Projeto acadêmico desenvolvido na disciplina **Aplicação de Mineração de Dados**, do curso de Ciência de Dados da Universidade Cruzeiro do Sul.

O trabalho utiliza análise exploratória de dados e técnicas de aprendizado de máquina para investigar características pessoais, familiares, sociais, comportamentais e escolares associadas ao desempenho acadêmico de estudantes.

## 🎯 Objetivo do projeto

Investigar se características dos estudantes e de seu contexto familiar, social e escolar, em conjunto com as notas obtidas nos períodos anteriores, fornecem informações relevantes para prever a aprovação ou reprovação.

O problema foi estruturado como uma tarefa de **classificação binária**, utilizando Python e bibliotecas de análise de dados e Machine Learning.

## 📊 Base de dados

Foi utilizado o conjunto **Student Performance**, no arquivo `student-mat.csv`, com informações de estudantes e seu desempenho em matemática.

| Característica | Quantidade |
|---|---:|
| Registros | 395 |
| Variáveis originais | 33 |
| Valores ausentes identificados | 0 |
| Registros duplicados identificados | 0 |

### Grupos de variáveis

- **Pessoais:** idade, sexo e localização.
- **Familiares:** escolaridade e ocupação dos pais, apoio familiar e composição familiar.
- **Escolares:** histórico de reprovações, tempo de estudo, apoio escolar e faltas.
- **Comportamentais e sociais:** frequência de saídas, consumo de álcool, atividades extracurriculares e acesso à internet.
- **Desempenho acadêmico:** notas dos períodos anteriores e nota final.

### Variável-alvo

A variável-alvo foi criada a partir da nota final `G3`, considerando a nota mínima 10 para aprovação:

- `0` — Reprovado (`G3 < 10`).
- `1` — Aprovado (`G3 >= 10`).

A coluna `G3` e outras variáveis derivadas do resultado final não foram utilizadas como atributos preditores.

## 🧠 Decisão metodológica: utilização de G1 e G2

Diferentemente de uma abordagem que busca prever o resultado antes do início do período letivo, este projeto considera as notas dos dois períodos anteriores (`G1` e `G2`) como variáveis preditoras.

Essa escolha foi feita porque o objetivo da modelagem final é estimar o resultado do estudante quando já existem informações sobre seu desempenho acadêmico parcial. As notas anteriores apresentam forte associação com a nota final e ajudam a contextualizar a previsão.

Portanto, os resultados devem ser interpretados considerando esse cenário de uso: **a previsão é realizada com G1 e G2 disponíveis**. Não se trata de uma previsão feita no início do ano letivo, antes da obtenção dessas notas.

## 🔎 Etapas do projeto

### 1. Análise exploratória de dados (EDA)

Realizada no notebook `01_analise_exploratoria.ipynb`, com:

- Inspeção da estrutura e dos tipos de dados.
- Verificação de valores ausentes e registros duplicados.
- Estatísticas descritivas das variáveis.
- Distribuições de variáveis numéricas e categóricas.
- Identificação de possíveis valores extremos pelo método IQR.
- Análises univariadas, bivariadas e multivariadas.
- Correlações de Pearson e Spearman.
- Comparações do desempenho acadêmico entre grupos de estudantes.

### 2. Preparação dos dados

Realizada no notebook `02_modelagem.ipynb`, incluindo:

- Criação da variável-alvo binária a partir de `G3`.
- Separação entre atributos preditores (`X`) e variável-alvo (`y`).
- Exclusão de `G3` e de variáveis derivadas do resultado para evitar vazamento de informações.
- Inclusão de `G1` e `G2` como preditoras.
- Divisão estratificada em treino (80%) e teste (20%).
- Padronização das variáveis numéricas com `StandardScaler`.
- Codificação das variáveis categóricas com `OneHotEncoder`.
- Integração do pré-processamento e do estimador em um `Pipeline`.

### 3. Modelagem e avaliação

Foram comparados três algoritmos:

- Regressão Logística.
- Árvore de Decisão.
- Random Forest.

A avaliação utilizou validação cruzada estratificada de 5 partições no conjunto de treinamento. As métricas consideradas foram:

- Accuracy.
- F1 Macro.
- Balanced Accuracy.
- Precisão para a classe Reprovado.
- Recall para a classe Reprovado.
- F1-score para a classe Reprovado.

Após a comparação por validação cruzada, os modelos também foram avaliados no conjunto de teste, separado previamente.

### 4. Interpretabilidade

Foram examinados os coeficientes da Regressão Logística e utilizadas técnicas SHAP para interpretar as contribuições dos atributos nas previsões do Random Forest.

Essas interpretações descrevem associações e contribuições preditivas do modelo. **Não demonstram relações de causa e efeito.**

## 🤖 Resultados

A divisão estratificada dos dados produziu:

| Conjunto | Registros | Proporção |
|---|---:|---:|
| Treinamento | 316 | 80% |
| Teste | 79 | 20% |

### Validação cruzada — médias de 5 partições

| Modelo | Accuracy | F1 Macro | Balanced Accuracy | Precisão Reprovados | Recall Reprovados | F1 Reprovados |
|---|---:|---:|---:|---:|---:|---:|
| Regressão Logística | 90,81% | 89,81% | 90,66% | 83,72% | 90,29% | 86,65% |
| Árvore de Decisão | 91,13% | 89,86% | 89,20% | 89,82% | 83,62% | 86,31% |
| Random Forest | **92,72%** | **91,94%** | **92,83%** | 86,18% | **93,24%** | **89,45%** |

O Random Forest apresentou os melhores resultados médios em Accuracy, F1 Macro e Balanced Accuracy, além do maior Recall e F1-score para a classe Reprovado.

### Avaliação no conjunto de teste

| Métrica | Regressão Logística | Árvore de Decisão | Random Forest |
|---|---:|---:|---:|
| Accuracy | 86,08% | 82,28% | 86,08% |
| F1 Macro | 85,12% | 80,93% | 85,12% |
| Balanced Accuracy | 87,66% | 82,87% | 87,66% |
| Precisão Reprovados | 72,73% | 68,75% | 72,73% |
| Recall Reprovados | 92,31% | 84,62% | 92,31% |
| F1 Reprovados | 81,36% | 75,86% | 81,36% |

A Regressão Logística e o Random Forest apresentaram resultados iguais nas métricas de teste exibidas. A escolha do **Random Forest como modelo principal** foi fundamentada principalmente no melhor desempenho médio observado durante a validação cruzada, e não em superioridade sobre a Regressão Logística no conjunto de teste.

Na matriz de confusão do Random Forest, foram identificados corretamente 24 dos 26 estudantes reprovados presentes no conjunto de teste, correspondendo a Recall de 92,31% para essa classe.

## 🏁 Modelo final

O Random Forest foi selecionado como modelo principal do projeto com base no desempenho médio da validação cruzada.

O pipeline completo foi salvo em:

```text
models/modelo_final_com_g1_g2.joblib
```

O arquivo inclui as etapas de pré-processamento e o modelo treinado, permitindo carregar o pipeline para realizar previsões com dados que apresentem a mesma estrutura esperada.

## 📁 Estrutura do repositório

```text
projeto-mineracao-dados/
├── data/
│   ├── raw/
│   │   └── student-mat.csv
│   └── processed/
├── models/
│   └── modelo_final_com_g1_g2.joblib
├── notebooks/
│   ├── 01_analise_exploratoria.ipynb
│   └── 02_modelagem.ipynb
├── reports/
├── src/
├── requirements.txt
├── .gitignore
└── README.md
```

## ⚙️ Como reproduzir o projeto

### 1. Clonar o repositório

```bash
git clone https://github.com/felipetamiozzo/projeto-mineracao-dados.git
cd projeto-mineracao-dados
```

### 2. Criar e ativar um ambiente virtual

No Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

No macOS ou Linux:

```bash
python -m venv .venv
source .venv/bin/activate
```

### 3. Instalar as dependências

```bash
python -m pip install -r requirements.txt
```

### 4. Executar os notebooks

Abra o projeto no Visual Studio Code ou Jupyter Notebook e execute os notebooks na seguinte ordem:

1. `notebooks/01_analise_exploratoria.ipynb`
2. `notebooks/02_modelagem.ipynb`

O arquivo de dados deve estar disponível em `data/raw/student-mat.csv`. A execução do notebook de modelagem gera o pipeline final no diretório `models/`.

## ⚠️ Limitações e considerações

- A base possui 395 registros, o que limita a quantidade de observações disponíveis para treinamento e teste.
- A distribuição das classes é desigual: aproximadamente 67,1% de aprovados e 32,9% de reprovados.
- As métricas do conjunto de teste estão sujeitas à variabilidade decorrente do tamanho reduzido dessa amostra.
- O modelo utiliza G1 e G2; portanto, sua aplicação pressupõe que essas notas já estejam disponíveis.
- A importância dos atributos e as contribuições SHAP não demonstram causalidade.
- Os resultados são acadêmicos e experimentais e não devem ser utilizados isoladamente para decisões sobre estudantes.

## 📚 Principais aprendizados

O desenvolvimento do projeto permitiu aplicar conceitos de:

- Análise exploratória e estatística descritiva.
- Preparação e transformação de dados.
- Engenharia de atributos e prevenção de vazamento de informações.
- Classificação supervisionada.
- Pipelines de pré-processamento.
- Validação cruzada e avaliação de modelos.
- Métricas para classes desbalanceadas.
- Interpretabilidade com coeficientes e SHAP.
- Organização e versionamento de projetos com Git e GitHub.

Um dos principais aprendizados foi compreender que a qualidade de um modelo não depende apenas de métricas elevadas, mas também de decisões metodológicas coerentes, avaliação adequada, interpretação cuidadosa e reconhecimento das limitações dos dados.

---

**Desenvolvido por Felipe Tamiozzo**  
Ciência de Dados | Análise de Dados | Machine Learning

[GitHub — felipetamiozzo](https://github.com/felipetamiozzo)
