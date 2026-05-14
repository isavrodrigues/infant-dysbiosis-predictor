Sprint #1 - 7/4: Explora¸c˜ao dos Dados
•⁠  ⁠Carregar e inspecionar Supplementary Data 2, 4 e 6
•⁠  ⁠Identificar missings, distribui¸c˜oes e valores ´unicos das vari´aveis cl´ınicas
•⁠  ⁠Analisar a distribui¸c˜ao dos clusters DMM (C1, C2, C3) e do desfecho adverso
•⁠  ⁠Fazer o merge dos trˆes datasets e exportar os datasets limpos para os dois modelos
•⁠  ⁠Definir e documentar as features finais de cada modelo



Sprint #2 - 14/4: Pr´e-processamento e Modelo 1
•⁠  ⁠Aplicar encoding nas vari´aveis categ´oricas (birth mode, feeding mode, etc.)
•⁠  ⁠Treinar Random Forest para classifica¸c˜ao de cluster (C1/C2/C3) com valida¸c˜ao
cruzada estratificada
•⁠  ⁠Avaliar desempenho com F1-Score ponderado e matriz de confus˜ao
•⁠  ⁠Analisar quais clusters s˜ao mais dif´ıceis de separar e documentar limita¸c˜oes


Sprint #3 - 28/4: Modelo 2
•⁠  ⁠Treinar XGBoost para predi¸c˜ao de desfecho adverso bin´ario aos 2 anos (n = 210,
holdout 20%)
•⁠  ⁠Aplicar valida¸c˜ao cruzada estratificada para lidar com o desbalanceamento de classes
•⁠  ⁠Avaliar com Recall, ROC-AUC e F1-Score
•⁠  ⁠Comparar desempenho com e sem a covariate antibiotics first 2 years
6


Sprint #4 - 5/5: Explainability
•⁠  ⁠Aplicar SHAP sobre o Modelo 1 e gerar summary plot e beeswarm plot de im-
portˆancia global
•⁠  ⁠Aplicar SHAP sobre o Modelo 2 e gerar waterfall plot de explica¸c˜oes locais por
paciente
•⁠  ⁠Identificar quais vari´aveis cl´ınicas tˆem maior peso preditivo em cada modelo
•⁠  ⁠Interpretar os resultados sob perspectiva cl´ınica: o que o modelo aprendeu faz sen-
tido biologicamente?



Sprint #5 - 12/5: Entreg´aveis Finais
•⁠  ⁠Escrever artigo no Medium documentando o problema, a abordagem e os principais
achados
•⁠  ⁠Gravar v´ıdeo demonstrando o pipeline funcionando e os resultados do SHAP
•⁠  ⁠Revisar o relat´orio final e preparar os slides para a apresenta¸c˜ao



portfolio de modelo supervisionado
mais simples ate o mais complexo
e fzr soh com variaveis clinicas

xgboost tirar cluster c2 e sem 

clarificar o objetivo disso e explicitar estrategiaue 