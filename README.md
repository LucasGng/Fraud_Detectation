# Detecção de Fraudes em Transações: Uma Abordagem de Classificação com Análise Exploratória e Pré-processamento

---

## 📄 Visão Geral do Projeto

Este projeto aborda o desafio da **detecção de fraudes em transações financeiras** utilizando técnicas de Data Science. O objetivo principal foi desenvolver um modelo robusto capaz de identificar transações fraudulentas a partir de um conjunto de dados real. O estudo detalha as etapas de análise exploratória de dados (EDA), pré-processamento e modelagem preditiva, culminando na construção de um classificador eficaz.

---

## 🚀 Tecnologias Utilizadas

* **Python**: Linguagem de programação principal.
* **Pandas**: Para manipulação e análise de dados.
* **NumPy**: Para operações numéricas de alta performance.
* **Scikit-learn**: Para Machine Learning (pré-processamento, seleção de características, modelagem e avaliação).
* **Matplotlib/Seaborn**: Para visualização de dados.

---

## 📊 Dataset

O dataset utilizado, **CreditCard Fraud Detection**, compreende transações de cartão de crédito realizadas na Europa em setembro de 2013. A principal característica e desafio deste dataset é o seu **severo desbalanceamento de classes**:

* **284.315** transações legítimas (Classe 0), representando aproximadamente **99,83%** do total.
* **492** transações fraudulentas (Classe 1), correspondendo a apenas **0,17%** do conjunto de dados.

Esse desequilíbrio exige abordagens cuidadosas de pré-processamento e avaliação para garantir a eficácia do modelo na detecção de fraudes.

---

## 🔍 Análise Exploratória de Dados (EDA)

A EDA foi crucial para entender a estrutura e as características do dataset.

### Distribuição das Classes
A análise inicial confirmou o **severo desbalanceamento** das classes, um ponto crítico para a modelagem.

| Classe              | Número de Instâncias | % Total de Instâncias |
| :------------------ | :------------------- | :-------------------- |
| 0 - Não Fraudulentas | 284.315              | 99,83%                |
| 1 - Fraudulentas    | 492                  | 0,17%                 |

### Análise Univariada

Variáveis como `Time` (tempo decorrido em segundos) e `Amount` (valor da transação em euros) foram analisadas. Novas variáveis, `Time Interval` (tempo em horas) e `Amount Interval` (intervalo de valores), foram criadas para facilitar a interpretação. As variáveis `V1` a `V28` são resultado de uma transformação PCA e, devido à sua natureza anonimizada, foram menos exploradas individualmente nesta fase.

* **Amount**: Grande concentração de transações com valores próximos a 5 euros. Transações com valores significativamente mais altos podem ser *outliers*.
* **Time**: Maior concentração de transações nos intervalos de 10 a 24 horas e de 30 a 48 horas. Ausência de *outliers* significativos.

### Análise Multivariada

Foram testadas diversas hipóteses para entender as relações entre as variáveis e a detecção de fraudes:

1.  **Relação entre Tempo e Valor da Transação**: Não foi encontrada correlação linear significativa entre `Time` e `Amount`.
2.  **Tempo das Transações Fraudulentas**: **Contrariando a hipótese inicial**, transações fraudulentas ocorreram em períodos *mais próximos da primeira transação* do que as legítimas.
3.  **Valor das Transações Fraudulentas**: **Corroborando a hipótese**, transações fraudulentas tendem a ter valores monetários menores. Transações não fraudulentas apresentam uma concentração muito maior de valores extremamente altos.
4.  **Intervalo de Tempo de Transações Fraudulentas vs. Não Fraudulentas**: **Confirmado**, os intervalos de tempo mais frequentes para fraudes (10h-15h, 24h-30h) são distintos dos para transações legítimas (36h-48h).
5.  **Intervalo de Valor de Transações Fraudulentas vs. Não Fraudulentas**: **Não corroborado**, os intervalos de valor mais frequentes são, em grande parte, os mesmos para ambas as classes.
6.  **Concentração de Transações por Intervalos de Valor e Tempo**: **Confirmado**, existe uma concentração de transações (ex: valores até 5 euros combinados com 10h-24h e 30h-48h) que representa aproximadamente 20% do dataset.

---

## 🛠️ Pré-processamento e Seleção de Características

### Pré-processamento dos Dados

* **Codificação de Variáveis Categóricas**: `Amount Interval` e `Time Interval` foram transformadas usando `OrdinalEncoder`.
* **Transformação de Normalização (Amount)**: A variável `Amount` foi submetida à `PowerTransformer` (método Yeo-Johnson) para reduzir a assimetria.
* **Escalonamento Min-Max**: Todas as variáveis numéricas foram escalonadas para o intervalo de 0 a 1 usando `MinMaxScaler`.

### Seleção de Características (Feature Selection)

O método `SelectKBest` foi utilizado para ranquear e selecionar as características mais relevantes. As seguintes colunas foram removidas: `Amount`, `V22`, `V23`, `V24`, `V25`, `V26`, `V28`, `Time`, `V15`, `V13`, `V27`, `V8`, `V20`, `Amount Interval`, `Time Interval`.

---

## 📈 Protocolo de Validação

### Métrica de Avaliação

O **F1-Score** foi escolhido como métrica principal devido ao desbalanceamento do dataset, por ponderar precisão e recall, sendo menos sensível ao domínio da classe majoritária do que a acurácia.

### Métodos de Validação

* **Hold-out**: 30% do dataset separado para validação.
* **Validação Cruzada Estratificada com 10 Dobras**: Para uma estimativa mais robusta e para manter a proporção das classes em cada dobra, essencial em datasets desbalanceados.

---

## 🧠 Modelo de Machine Learning

O modelo de **Árvore de Decisão** foi selecionado pela sua simplicidade, robustez e agilidade de treinamento/inferência, características cruciais na detecção de fraudes.

**Parâmetros Definidos:**
* **Critério de Divisão (`criterion`)**: Entropia
* **Profundidade Máxima (`max_depth`)**: 5 (para controlar a complexidade e prevenir *overfitting*)
* **Estado Aleatório (`random_state`)**: 42 (para reprodutibilidade)

---

## 🎯 Resultados Obtidos

### Avaliação com Hold-out

| Métrica   | Valor    |
| :-------- | :------- |
| Precision | 81,63%   |
| Recall    | 81,08%   |
| F1-Score  | 81,35%   |

![Matriz de Confusão do Hold-out](https://drive.google.com/file/d/18TECfL9roQotaOatFju33IUTXcfXPfly/view)

### Avaliação com Validação Cruzada Estratificada (10 Dobras)

| Métrica       | Valor Médio |
| :------------ | :---------- |
| Precision Médio | 89,56%      |
| Recall Médio    | 77,84%      |
| F1-Score Médio  | 83,21%      |

![Matriz de Confusão Média das 10 Dobras](https://drive.google.com/file/d/1w4EIJS6k4AV7bi2LwT1YISoK06l1CPwH/view)

Os resultados da validação cruzada demonstram um **F1-Score médio consistente e promissor** de 83,21%, indicando a eficácia do modelo na detecção de fraudes, mesmo com o desafio do desbalanceamento de classes.

---

## 🎉 Conclusão

Este estudo reforça que a excelência em Data Science não se limita à escolha de algoritmos complexos, mas reside primordialmente na **meticulosa atenção às fases de exploração e transformação dos dados**. Um modelo de Árvore de Decisão, considerado "simples", alcançou um desempenho notável devido ao pré-processamento e tratamento adequados do dataset desbalanceado. A qualidade dos dados e a preparação cuidadosa podem ser mais determinantes para o sucesso preditivo do que a complexidade do algoritmo em si.

---

## 📚 Referências

* Pandas Development Team. "pandas: powerful Python data analysis and manipulation," 2024. [Online]. Available: https://pandas.pydata.org
* scikit-learn developers. "scikit-learn: Machine Learning in Python," 2024. [Online]. Available: https://scikit-learn.org/stable/
* NumPy Development Team. "NumPy: the fundamental package for scientific computing with Python," 2024. [Online]. Available: https://numpy.org
* ULB MLG. "Credit Card Fraud Detection Dataset," Kaggle, 2018. [Online]. Available: https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud

---