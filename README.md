# Predição e Explicabilidade do Risco Clínico Infantil

Projeto desenvolvido para a disciplina **Artificial Intelligence in Medicine and Healthcare** no Insper


---

## objetivo

É possível prever o estado do microbioma intestinal e o risco de desenvolver alergias, eczema ou asma até os 2 anos usando apenas dados clínicos disponíveis no momento do nascimento, sem sequenciamento genético?

Para investigar essa hipótese, dois modelos supervisionados serão treinados:

- **Modelo 1:** Random Forest para classificar o perfil do microbioma em três grupos: saudável, intermediário ou disbiótico
- **Modelo 2:** XGBoost para prever o desenvolvimento de alergias, eczema ou asma até os 2 anos

Ambos os modelos serão interpretados com SHAP (Explainable AI), identificando quais fatores clínicos do nascimento mais influenciam o risco.

---

## dados

Os dados utilizados são públicos e foram obtidos do estudo:

> Jarman, J.B., Torres, P.J., Stromberg, S. et al. **Bifidobacterium deficit in United States infants drives prevalent gut dysbiosis.** *Communications Biology* 8, 867 (2025). https://doi.org/10.1038/s42003-025-08274-7

Os arquivos utilizados são os dados suplementares disponibilizados junto ao artigo:

| arquivo | descrição |
|---|---|
| Supplementary Data 2 | metadados clínicos de 412 bebês e abundância de 550 espécies bacterianas |
| Supplementary Data 4 | abundância de genes de resistência antimicrobiana (AMR) e fatores de virulência (VF) |
| Supplementary Data 6 | desfechos clínicos aos 2 anos para 210 famílias participantes |

---

## estrutura do repositório

```
infant-dysbiosis-predictor/
├── data/
│   ├── Supplementary Data 2.xlsx
│   ├── Supplementary Data 4.xlsx
│   ├── Supplementary Data 6.xlsx
│   └── ...
├── notebooks/
│   ├── sprint1_eda.ipynb
│   ├── sprint2_model1.ipynb
│   ├── sprint3_model2.ipynb
│   └── sprint4_shap.ipynb
├── .gitignore
├── LICENSE
└── README.md
```

---

## licença dos dados

Os dados originais estão licenciados sob **Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)**.

Esta licença permite uso não comercial, compartilhamento e reprodução em qualquer formato, desde que o crédito aos autores originais seja dado e um link para a licença seja fornecido. Não é permitido compartilhar material adaptado derivado do artigo.

Para mais informações: http://creativecommons.org/licenses/by-nc-nd/4.0/