🧠 Como um modelo "aprende"?

Aprender, aqui, significa ajustar uma função aos dados de modo a errar o mínimo possível. O algoritmo define uma função de custo (tipicamente o erro quadrático médio, o MSE) que mede o tamanho dos erros, e então busca os parâmetros que minimizam esse custo.

Na regressão linear simples, por exemplo, o modelo procura a reta y = w₀ + w₁·x que melhor representa os dados — ajustando o intercepto w₀ e a inclinação w₁ para reduzir a diferença entre os valores previstos e os reais.

Na regressão linear simples, o modelo tenta encontrar a melhor reta para representar os dados.

A reta é:

y = w₀ + w₁·x

x → valor de entrada.
y → valor que queremos prever.
w₀ → intercepto: onde a reta começa no eixo Y.
w₁ → inclinação: indica o quanto Y muda quando X aumenta 1 unidade.

Exemplo

Imagine que queremos prever o preço de uma casa baseado no seu tamanho.

x = tamanho da casa
y = preço
w₀ = preço estimado quando o tamanho é 0
w₁ = quanto o preço aumenta para cada m² adicional

O algoritmo testa diferentes valores de w₀ e w₁ até encontrar uma reta que fique o mais próxima possível dos pontos reais.

Em outras palavras:

A regressão linear aprende a melhor reta para fazer previsões.

E "reduzir a diferença entre previsto e real" significa simplesmente diminuir o erro das previsões.

- 💡 Insight — viés e variância
Todo modelo equilibra dois erros: viés (simples demais, não captura o padrão → underfitting) e variância (complexo demais, decora o treino e falha em dados novos → overfitting). O objetivo não é zerar o erro no treino, e sim generalizar bem para dados que o modelo nunca viu.



🧰 Panorama dos algoritmos de regressão
Não existe um algoritmo "melhor" universal — este é o famoso teorema do no free lunch. Cada problema pede experimentação. Estes são os principais:

Algoritmo	Ideia central	Classe (sklearn)
Regressão Linear	Ajusta uma reta/plano aos dados	LinearRegression
Polinomial	Linear com termos de grau maior (curvas)	PolynomialFeatures
Ridge / Lasso / Elastic Net	Linear com regularização (controla overfitting)	Ridge, Lasso, ElasticNet
KNN	Média dos vizinhos mais próximos	KNeighborsRegressor
SVR	Máquina de vetores de suporte para regressão	SVR
Árvore de decisão	Divide os dados em regiões por regras	DecisionTreeRegressor
Ensembles	Combinam vários modelos (Random Forest, boosting)	RandomForestRegressor …
Os ensembles — Random Forest, Gradient Boosting, XGBoost, LightGBM, CatBoost — costumam liderar em dados tabulares e têm uma página dedicada neste módulo. Redes neurais também podem ser usadas, mas são mais indicadas para dados de alta dimensão (imagem, texto, áudio).

📊 Como avaliamos um modelo de regressão?
Como a saída é um número, medimos o erro entre o previsto e o real. As três métricas mais usadas:

MAE — erro médio absoluto; intuitivo e robusto.
RMSE — raiz do erro quadrático médio; pune mais os erros grandes.
R² — fração da variância explicada pelo modelo (quanto mais perto de 1, melhor).

✅ Dica — comece simples
Sempre comece por um modelo simples, como a regressão linear, para ter uma linha de base. Só faz sentido partir para modelos mais complexos se eles superarem essa referência. Simplicidade primeiro, sofisticação depois.

🎥 Vídeos explicativos
🇧🇷 Introdução aos modelos de regressão — Microsoft Learn (série ML para iniciantes, em português).
🌐 A Gentle Introduction to Machine Learning — StatQuest (os conceitos, sem jargão).
🌐 Linear Regression, Clearly Explained — StatQuest (mínimos quadrados e R²).
📚 Referências
scikit-learn — Supervised learning (índice oficial dos algoritmos).
scikit-learn — Linear Models (regressão linear, Ridge, Lasso, Elastic Net).
Google ML Crash Course — Linear Regression (fundamentos, com gradiente descendente).
Machine Learning: Conceitos e Modelos — Aprendizado Supervisionado — Tatiana Escovedo (PT).
Introdução ao Machine Learning — capítulo de Regressão (livro aberto em português).
Regressão, Classificação e Clustering — Green Tecnologia (PT, panorama das técnicas).
🎒 Resumo de bolso
Aprendizado supervisionado: aprender com exemplos rotulados (X → y).
Regressão prevê números; classificação prevê categorias.
Vocabulário: preditora (X), alvo Sim, treino/teste, .fit()/.predict().
Aprender = minimizar o erro (função de custo, ex. MSE).
Cuidado com o par viés × variância (under/overfitting).
Há muitos algoritmos (linear, KNN, SVR, árvores, ensembles) — no free lunch: teste e compare.
Avalie com MAE, RMSE e R². Comece simples.