# 📝 Respostas da Atividade Computacional 5 — Previsão do Valor do Seguro com SVR

---

## ✏️ Questão 1 — Revisão: carregar e dividir a base de dados

**Status:** Não pontuada (revisão)

**Código implementado:**
```python
import warnings
warnings.filterwarnings("ignore")

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from IPython.display import display

from sklearn.model_selection import train_test_split
from sklearn.compose import ColumnTransformer, TransformedTargetRegressor
from sklearn.preprocessing import OneHotEncoder, StandardScaler
from sklearn.pipeline import Pipeline
from sklearn.svm import SVR
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score
from sklearn.inspection import PartialDependenceDisplay

RANDOM_STATE = 42
plt.rcParams["figure.figsize"] = (8, 5)
plt.rcParams["axes.grid"] = True

# 1. Carregar o dataset Insurance
url = "https://raw.githubusercontent.com/krish1407/Medical-Cost-Personal-Datasets/master/insurance.csv"
df = pd.read_csv(url)

# 2. Separar entradas e alvo
X = df.drop(columns=["charges"])
y = df["charges"]

# 3. Definir as variáveis por tipo
numeric_features = ["age", "bmi", "children"]
categorical_features = ["sex", "smoker", "region"]

# 4. Separar treino e teste
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=RANDOM_STATE
)

# 5. Conferir os resultados
print("Tamanho da base completa:", df.shape)
print("X_train:", X_train.shape)
print("X_test:", X_test.shape)
print("y_train:", y_train.shape)
print("y_test:", y_test.shape)
```

---

## ✏️ Questão 2 — Pré-processamento das variáveis categóricas (2,5 pontos)

**Código implementado:**
```python
# 1. Criar o OneHotEncoder
codificador_categorico = OneHotEncoder(handle_unknown="ignore", sparse_output=False)

# 2. Criar o pré-processador sem normalização
preprocessador_sem_normalizacao = ColumnTransformer(
    transformers=[
        ("num", "passthrough", numeric_features),
        ("cat", codificador_categorico, categorical_features)
    ],
    remainder="drop"
)

# 3. Ajustar e transformar X_train
X_train_transformado = preprocessador_sem_normalizacao.fit_transform(X_train)

# 4. Recuperar nomes das colunas transformadas
nomes_features_sem_norm = preprocessador_sem_normalizacao.get_feature_names_out()

print("Número de colunas originais:", X_train.shape[1])
print("Número de colunas após OneHotEncoder:", X_train_transformado.shape[1])
print("\nNomes das colunas após o pré-processamento:")
print(nomes_features_sem_norm)
```

### 🧠 Pergunta de múltipla escolha — Questão 2

**Por que usamos `OneHotEncoder()` nas variáveis categóricas?**

✅ **Resposta: B. Para transformar categorias de texto em colunas numéricas binárias.**

**Justificativa:** O `OneHotEncoder()` cria colunas binárias (0 ou 1) para cada categoria única. Por exemplo, a variável `smoker` com valores "yes" e "no" é transformada em duas colunas binárias. Isso é necessário porque o SVR (e a maioria dos algoritmos de ML) só consegue processar dados numéricos, não texto.

---

## ✏️ Questão 3 — Normalização para uso do SVR (2,5 pontos)

**Código implementado:**
```python
# 1. Criar o OneHotEncoder
onehot = OneHotEncoder(handle_unknown="ignore", sparse_output=False)

# 2. Criar o pré-processador com normalização
preprocessador_com_normalizacao = ColumnTransformer(
    transformers=[
        ("num", StandardScaler(), numeric_features),
        ("cat", onehot, categorical_features)
    ],
    remainder="drop"
)

# 3. Visualizar o efeito da transformação
X_train_norm = preprocessador_com_normalizacao.fit_transform(X_train)
nomes_features_norm = preprocessador_com_normalizacao.get_feature_names_out()

print("Número de colunas após o pré-processamento:", X_train_norm.shape[1])
print("\nNomes das colunas:")
print(nomes_features_norm)
```

### 🧠 Pergunta de múltipla escolha — Questão 3

**Por que a normalização é especialmente importante no SVR com kernel RBF?**

✅ **Resposta: B. Porque o SVR com RBF depende de distâncias entre os pontos.**

**Justificativa:** O kernel RBF (Radial Basis Function) calcula a similaridade entre pontos de dados usando uma função baseada na distância euclidiana. Se as variáveis estiverem em escalas diferentes (ex: `age` varia de 18-64, `bmi` de 15-50, `children` de 0-5), aquelas com valores maiores dominarão o cálculo da distância, prejudicando o modelo. O `StandardScaler()` padroniza as variáveis para média 0 e desvio padrão 1, colocando todas na mesma escala de importância.

---

## ✏️ Questão 4 — Montagem do modelo SVR com `Pipeline` (2,5 pontos)

**Código implementado:**
```python
# 1. Criar o pipeline do SVR
modelo_svr = Pipeline(steps=[
    ("preprocessamento", preprocessador_com_normalizacao),
    ("modelo", SVR(kernel="rbf", C=100000, epsilon=0.01, gamma="scale"))
])

# 2. Treinar o modelo
modelo_svr.fit(X_train, y_train)

# 3. Fazer previsões
pred_train_svr = modelo_svr.predict(X_train)
pred_test_svr = modelo_svr.predict(X_test)

print("Modelo SVR treinado com sucesso.")
print("A variável-alvo charges foi mantida na escala original.")
```

### 🧠 Pergunta de múltipla escolha — Questão 4

**Na atividade principal, qual transformação está sendo aplicada antes do `SVR()`?**

✅ **Resposta: B. A codificação das categóricas e a normalização das variáveis numéricas de entrada.**

**Justificativa:** O pipeline usa o `preprocessador_com_normalizacao` que aplica:
- `StandardScaler()` nas variáveis numéricas (`age`, `bmi`, `children`)
- `OneHotEncoder()` nas variáveis categóricas (`sex`, `smoker`, `region`)

A variável-alvo `charges` **não** é transformada nesta etapa — ela permanece na escala original.

---

## ✏️ Questão 5 — Métricas de desempenho do SVR (2,5 pontos)

**Código implementado:**
```python
# 1. Calcular métricas
mae_train = mean_absolute_error(y_train, pred_train_svr)
mae_test = mean_absolute_error(y_test, pred_test_svr)

rmse_train = mean_squared_error(y_train, pred_train_svr) ** 0.5
rmse_test = mean_squared_error(y_test, pred_test_svr) ** 0.5

r2_train = r2_score(y_train, pred_train_svr)
r2_test = r2_score(y_test, pred_test_svr)

# 2. Montar tabela de resultados
resultados_svr = pd.DataFrame({
    "Modelo": ["SVR RBF"],
    "MAE treino": [mae_train],
    "MAE teste": [mae_test],
    "RMSE treino": [rmse_train],
    "RMSE teste": [rmse_test],
    "R² treino": [r2_train],
    "R² teste": [r2_test]
})

display(resultados_svr)

# 3. Gráfico: valores reais vs previstos
plt.figure()
plt.scatter(y_test, pred_test_svr, alpha=0.6)

min_val = min(y_test.min(), pred_test_svr.min())
max_val = max(y_test.max(), pred_test_svr.max())
plt.plot([min_val, max_val], [min_val, max_val], linestyle="--")

plt.xlabel("Valor real do seguro")
plt.ylabel("Valor previsto pelo SVR")
plt.title("SVR: valores reais vs previstos")
plt.show()

# 4. Insight
print("Insight:")
print("Compare treino e teste. Se o erro de teste for muito maior que o erro de treino, pode haver overfitting.")
print("Se ambos os erros forem altos, o modelo pode estar subajustado ou precisar de ajuste de hiperparâmetros.")
```

### 🧠 Pergunta de múltipla escolha — Questão 5

**Qual métrica penaliza mais fortemente erros grandes?**

✅ **Resposta: B. RMSE**

**Justificativa:** O RMSE (Root Mean Squared Error) eleva os erros ao quadrado antes de calcular a média e depois tira a raiz quadrada. Isso significa que erros maiores têm peso desproporcionalmente maior no resultado final. Por exemplo, um erro de 1000 contribui 1.000.000 para o quadrado médio, enquanto um erro de 100 contribui apenas 10.000. Já o MAE (Mean Absolute Error) trata todos os erros de forma linear e proporcional.

---

## ⭐ Atividade Extra A — GridSearchCV

**Código implementado:**
```python
# 1. Definir a grade de hiperparâmetros
param_grid = {
    "modelo__C": [1, 10, 100, 1000, 10000],
    "modelo__epsilon": [0.05, 0.1, 0.2, 0.5],
    "modelo__gamma": ["scale", 0.01, 0.05, 0.1]
}

# 2. Definir validação cruzada
cv = KFold(n_splits=5, shuffle=True, random_state=RANDOM_STATE)

# 3. Criar o GridSearchCV
grid_search = GridSearchCV(
    modelo_svr,
    param_grid,
    cv=cv,
    scoring="neg_mean_squared_error",
    n_jobs=-1
)

# 4. Treinar a busca
grid_search.fit(X_train, y_train)

# 5. Mostrar os melhores parâmetros
print("Melhores hiperparâmetros:")
print(grid_search.best_params_)

# 6. Avaliar o melhor modelo
modelo_svr_ajustado = grid_search.best_estimator_
pred_test_svr_ajustado = modelo_svr_ajustado.predict(X_test)

mae_test_ajustado = mean_absolute_error(y_test, pred_test_svr_ajustado)
rmse_test_ajustado = mean_squared_error(y_test, pred_test_svr_ajustado) ** 0.5
r2_test_ajustado = r2_score(y_test, pred_test_svr_ajustado)

comparacao_grid = pd.DataFrame({
    "Modelo": ["SVR base", "SVR com GridSearchCV"],
    "MAE teste": [mae_test, mae_test_ajustado],
    "RMSE teste": [rmse_test, rmse_test_ajustado],
    "R² teste": [r2_test, r2_test_ajustado]
})

display(comparacao_grid)
```

---

## ⭐ Atividade Extra B — Gráfico de Dependência Parcial

**Código implementado:**
```python
# 1. Escolher o modelo para interpretação
if "modelo_svr_ajustado" in globals():
    modelo_para_pdp = modelo_svr_ajustado
    nome_modelo_pdp = "SVR ajustado com GridSearchCV"
else:
    modelo_para_pdp = modelo_svr
    nome_modelo_pdp = "SVR base"

# 2. Plotar dependência parcial
features_para_plotar = ["age", "bmi"]

PartialDependenceDisplay.from_estimator(
    modelo_para_pdp,
    X_test,
    features=features_para_plotar,
    kind="average"
)

plt.suptitle("Dependência parcial — previsão do valor do seguro", y=1.05)
plt.show()

print("\nInsight:")
print("Observe se a previsão média do seguro aumenta ou diminui quando age e bmi variam.")
print("Como o SVR com RBF é não linear, a curva pode não ser uma reta.")
```

**Interpretação:** O gráfico de dependência parcial mostra como a previsão média do seguro muda quando variamos uma variável específica (como `age` ou `bmi`), mantendo as demais constantes. Para `age`, espera-se uma tendência de aumento com a idade, refletindo maior risco. Para `bmi`, a relação pode ser não linear, com valores extremos (muito baixos ou muito altos) associados a custos maiores.

---

## ⭐ Atividade Extra C — Comparação com `TransformedTargetRegressor`

**Código implementado:**
```python
# 1. Criar um novo pipeline SVR igual ao da atividade principal
svr_pipeline_para_alvo = Pipeline(steps=[
    ("preprocessamento", preprocessador_com_normalizacao),
    ("modelo", SVR(kernel="rbf", C=10, epsilon=0.1, gamma="scale"))
])

# 2. Envolver o pipeline com TransformedTargetRegressor
modelo_svr_com_alvo_transformado = TransformedTargetRegressor(
    regressor=svr_pipeline_para_alvo,
    transformer=StandardScaler()
)

# 3. Treinar o modelo
modelo_svr_com_alvo_transformado.fit(X_train, y_train)

# 4. Fazer previsões
pred_test_svr_alvo_transformado = modelo_svr_com_alvo_transformado.predict(X_test)

# 5. Calcular métricas
mae_test_alvo_transformado = mean_absolute_error(y_test, pred_test_svr_alvo_transformado)
rmse_test_alvo_transformado = mean_squared_error(y_test, pred_test_svr_alvo_transformado) ** 0.5
r2_test_alvo_transformado = r2_score(y_test, pred_test_svr_alvo_transformado)

# 6. Comparar com o SVR principal
comparacao_alvo = pd.DataFrame({
    "Modelo": [
        "SVR sem transformar charges",
        "SVR com TransformedTargetRegressor"
    ],
    "MAE teste": [mae_test, mae_test_alvo_transformado],
    "RMSE teste": [rmse_test, rmse_test_alvo_transformado],
    "R² teste": [r2_test, r2_test_alvo_transformado]
})

display(comparacao_alvo)

# 7. Gráfico comparativo
plt.figure()
plt.bar(comparacao_alvo["Modelo"], comparacao_alvo["RMSE teste"])
plt.ylabel("RMSE no teste")
plt.title("Comparação: SVR com e sem transformação do alvo")
plt.xticks(rotation=15)
plt.show()

# 8. Insight automático
print("Insight:")
if rmse_test_alvo_transformado < rmse_test:
    print("Neste teste, transformar o alvo reduziu o RMSE no conjunto de teste.")
    print("Isso indica que o SVR pode ter se beneficiado de aprender o alvo charges em uma escala padronizada.")
elif rmse_test_alvo_transformado > rmse_test:
    print("Neste teste, transformar o alvo aumentou o RMSE no conjunto de teste.")
    print("Isso indica que, com estes hiperparâmetros, a transformação do alvo não melhorou o desempenho.")
else:
    print("Neste teste, os dois modelos apresentaram RMSE muito semelhante.")
```

**Interpretação:** O `TransformedTargetRegressor()` aplica uma transformação (neste caso, `StandardScaler()`) na variável-alvo `charges` durante o treinamento e inverte automaticamente a transformação nas previsões. Isso pode ser útil quando o alvo tem uma distribuição muito assimétrica ou valores em escala muito ampla. A comparação das métricas (MAE, RMSE, R²) indica se essa estratégia melhorou o desempenho do modelo.

---

## 📊 Resumo das Respostas

| Questão | Pontos | Resposta |
|---------|--------|----------|
| Questão 1 | 0,0 (revisão) | Código implementado |
| Questão 2 | 2,5 | **B** — OneHotEncoder transforma categorias em colunas binárias |
| Questão 3 | 2,5 | **B** — SVR com RBF depende de distâncias entre pontos |
| Questão 4 | 2,5 | **B** — Codificação de categóricas + normalização de numéricas |
| Questão 5 | 2,5 | **B** — RMSE penaliza mais fortemente erros grandes |
| **Total** | **10,0** | |

---

## 🔑 Conceitos-Chave Aprendidos

1. **SVR (Support Vector Regression):** Algoritmo de regressão baseado em Support Vector Machines, que busca encontrar uma função que se desvie dos valores reais no máximo por um valor ε (epsilon).

2. **Kernel RBF:** Função de base radial que permite ao SVR capturar relações não lineares mapeando os dados para um espaço de maior dimensionalidade.

3. **Normalização (StandardScaler):** Essencial para SVR porque o algoritmo usa cálculos de distância. Sem normalização, variáveis com escalas maiores dominam o modelo.

4. **OneHotEncoder:** Transforma variáveis categóricas em colunas binárias, permitindo que algoritmos numéricos processem dados categóricos.

5. **Pipeline:** Organiza o fluxo de pré-processamento e modelo, evitando vazamento de dados ao garantir que transformações sejam ajustadas apenas no treino.

6. **Métricas de avaliação:**
   - **MAE:** Erro absoluto médio (interpretação direta)
   - **RMSE:** Raiz do erro quadrático médio (penaliza erros grandes)
   - **R²:** Proporção da variância explicada pelo modelo

7. **GridSearchCV:** Busca exaustiva de hiperparâmetros com validação cruzada para encontrar a melhor combinação.

8. **PartialDependenceDisplay:** Ferramenta de interpretabilidade que mostra como as previsões mudam com variações em uma variável específica.

9. **TransformedTargetRegressor:** Envolve o modelo e aplica transformação na variável-alvo, útil quando o alvo tem distribuição assimétrica ou escala muito ampla.
