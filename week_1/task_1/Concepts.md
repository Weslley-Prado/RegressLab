🎯 O que é aprendizado supervisionado?


No aprendizado supervisionado, ensinamos o modelo a partir de exemplos rotulados: para cada conjunto de entradas, já sabemos a resposta certa. O modelo estuda esses exemplos históricos e aprende uma função que mapeia entradas em saídas, para depois prever a resposta de dados novos.

 - Resumindo: temos um dataset de exemplo inicial, para que o modelo possa aprender. Depois de treinar o modelo, verificamos se o modelo seguiu o padrão ensinado. *O modelo estuda esses exemplos históricos e aprende uma função que mapeia entradas em saídas, para depois prever a resposta de dados novos.*

 O aprendizado supervisionado pode ser subdivido em duas partes ou problemas que ele soluciona:

 1. **Regressão:** faz previsão de um *valor numérico contínuo*.
 - Exemplo: Preço de um imóvel, a temperatura de amanhã, o consumo de combustível.
 
 2.**Classificação:** faz previsão de categoria
 - Exemplo: spam ou não-spam, aprovado ou reprovado.


**Foco no Módulo: Regressão**

Teremos uma saída numérica e a qualidade será medida pela distância entre o que ele previu e o valor real.



📚 Vocabulário essencial

Variável preditora (X) => entrada usada para prever, também é chamada de feature ou variável independente.

Modelo => a função matemática que o algoritmo ajusta aos dados.

Treino/teste:  Parte dos dados usada para o modelo aprender / parte reservada para avaliá-lo em dados novos.

.fit() / .predict()	Os dois verbos do scikit-learn: ajustar o modelo aos dados e prever novos valores.
