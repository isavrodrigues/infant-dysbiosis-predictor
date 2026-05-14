# Resumo do Projeto — Infant Dysbiosis Predictor

Disciplina: Artificial Intelligence in Medicine and Healthcare — Insper

---

## 1. Proposta

**Pergunta central:** é possível prever o estado do microbioma intestinal de um bebê e o risco de desenvolver alergias, eczema ou asma até os 2 anos usando apenas dados clínicos disponíveis no momento do nascimento, sem sequenciamento genético?

Para investigar essa hipótese, dois modelos supervisionados são treinados:

| Modelo | Algoritmo | Problema | Target | n |
|---|---|---|---|---|
| Modelo 2 | Random Forest | Classificação multiclasse | Cluster DMM (C1/C2/C3) | 412 |
| Modelo 1 | XGBoost | Classificação binária | Desfecho adverso aos 2 anos (0/1) | 208 |

Ambos os modelos serão interpretados com **SHAP**, identificando quais variáveis clínicas do nascimento mais influenciam cada predição.

---

## 2. Dados

Fonte: dados suplementares do artigo científico:

> Jarman et al. **Bifidobacterium deficit in United States infants drives prevalent gut dysbiosis.** *Communications Biology* 8, 867 (2025).

| Arquivo | Conteúdo | Amostras | Colunas |
|---|---|---|---|
| Supplementary Data 2 | Metadados clínicos + abundância relativa de 550 espécies bacterianas | 412 | 564 |
| Supplementary Data 4 | Abundância de genes de resistência antimicrobiana (AMR) e fatores de virulência (VF) | 412 | 3 |
| Supplementary Data 6 | Desfechos clínicos aos 2 anos (alergias, eczema, asma) | 210 | 7 |

Os dados do Data 6 correspondem a um subconjunto de 210 famílias com follow-up completo (de 412 iniciais).

---

## 3. Plano de Sprints

| Sprint | Data | Objetivo |
|---|---|---|
| Sprint 1.1 | — | Inspeção dos três arquivos: estrutura, missings, distribuições e exportação dos datasets |
| Sprint 2 | 14/04 | Pré-processamento e Modelo 2 (Random Forest — cluster) |
| Sprint 3 | 28/04 | Modelo 1 (XGBoost — desfecho adverso) |
| Sprint 4 | 05/05 | Explicabilidade com SHAP |
| Sprint 5 | 12/05 | Entregáveis finais: artigo Medium, vídeo, slides |

---

## 4. O que foi feito

### Sprint 1.1 — `sprint1.1_inspecao_dados.ipynb`

#### Por que esse notebook existe?

A Sprint 1 (EDA) tinha como objetivo principal preparar os dados para os modelos. A Sprint 1.1 nasceu da necessidade de uma **inspeção mais sistemática e documentada** de cada arquivo de forma independente — antes de qualquer decisão de feature engineering. O objetivo foi garantir que nenhuma característica estrutural dos dados passasse despercebida antes do pré-processamento.

#### O que foi feito em detalhes:

**Supplementary Data 2 — metadados clínicos + microbioma**

- Identificação das 14 colunas clínicas e das 550 colunas de microbioma
- Estatísticas descritivas completas por variável (tipo, n válidos, média, mediana, std, p25, p75)
- Confirmação do único missing relevante: `pH` com 50% ausente → **decisão: descartar como feature**
- Análise de valores únicos de todas as variáveis categóricas com contagens e percentuais:
  - `birth_mode`: Vaginal (66%), C-Section (34%)
  - `feeding_mode`: Breast (54%), Mixed (33%), Formula (13%)
  - `baby_antibiotics`: 100% "No" → **descartada por variância zero**
  - `mother_antibiotics`: 97.6% "No", 2.4% "Yes"
  - `baby_sex`: Male (52%), Female (48%)
  - `baby_ethnicity`: Not Hispanic (85%), Hispanic (15%)
  - `baby_race`: White (91%), Asian (5%), Black (3%), outros → agrupar minoria em "Other"
- Crosstabs proporcionais (variável × cluster DMM): identifica visualmente quais variáveis têm associação com o cluster
- Distribuição de `Normed Age at Collection (days)`: média 54 dias, mediana 53 dias, range 6–155
- Tabela de decisão consolidada de todas as variáveis (feature / target / descartar + motivo)

**Supplementary Data 4 — AMR e VF**

- Confirmação: sem missings, 412 amostras, 3 colunas
- Estatísticas de `amr_gene_sum_abundance` e `vf_sum_abundance`
- Correlação entre AMR e VF (r ≈ baixo — capturam aspectos independentes)
- Histogramas e scatter plot das duas variáveis
- **Decisão: ambas incluídas como features numéricas nos dois modelos**

**Supplementary Data 6 — desfechos aos 2 anos**

- Confirmação: 210 amostras, 7 colunas, sem missings formais
- Identificação de encoding inconsistente: `eczema` tem valor `0` em 4 linhas (deve ser "No") → **tratado no pré-processamento**
- Distribuição do desfecho adverso: 62 positivos de 208 (29.8%) — desbalanceamento ~30/70
- Desfechos por tipo: alergias (26), eczema (44), asma (7) — sobreposição possível
- Uso de antibióticos nos primeiros 2 anos: 53.8% das crianças → **incluída como covariate no Modelo 1**

**Análise integrada: cluster DMM × desfecho adverso (n=210)**

| Cluster | n | Positivos | Taxa | Risco relativo vs C1 |
|---|---|---|---|---|
| C1 (saudável) | 52 | 10 | 19.2% | 1.00x |
| C2 (intermediário) | 68 | 23 | 33.8% | 1.76x |
| C3 (alto risco) | 90 | 30 | 33.3% | 1.73x |

Confirmação empírica de que o cluster é um intermediário relevante entre fatores clínicos do nascimento e o desfecho adverso — justifica os dois modelos em sequência.

**Merge e exportação dos datasets finais**

Dois datasets exportados para `/data/processed/`:

| Arquivo | Modelo | n | Features | Target |
|---|---|---|---|---|
| `model2_cluster_dataset.csv` | Modelo 2 | 412 | 9 | `dmm_clusters_taxa` (C1/C2/C3) |
| `model1_outcome_dataset.csv` | Modelo 1 | 208 | 10 | `adverse_outcomes` (0/1) |

Schema das features salvo em `features_schema.json`.

---

## 5. Features Finais

### Features compartilhadas (ambos os modelos)

| Feature | Tipo | Fonte | Justificativa |
|---|---|---|---|
| `birth_mode` | Categórica binária | Data 2 | Parto cesareo altera colonização inicial do microbioma |
| `feeding_mode` | Categórica 3 classes | Data 2 | Amamentação exclusiva é o principal driver do cluster C1 |
| `mother_antibiotics` | Categórica binária | Data 2 | Antibióticos maternos alteram microbioma transferido ao bebê |
| `baby_sex` | Categórica binária | Data 2 | Diferenças biológicas no desenvolvimento imune e microbiano |
| `baby_ethnicity` | Categórica binária | Data 2 | Associada a diferenças na composição do microbioma |
| `baby_race` | Categórica multiclasse | Data 2 | Covariável demográfica |
| `Normed Age at Collection (days)` | Numérica contínua | Data 2 | Microbioma muda rapidamente nos primeiros meses de vida |
| `amr_gene_sum_abundance` | Numérica contínua | Data 4 | Perfil de resistência antimicrobiana |
| `vf_sum_abundance` | Numérica contínua | Data 4 | Carga de fatores de virulência |

### Feature exclusiva do Modelo 1

| Feature | Tipo | Justificativa |
|---|---|---|
| `antibiotics_first_2_years` | Binária (0/1) | Covariate do paper original — controla confundimento entre cluster e desfecho |

### Variáveis descartadas

| Variável | Motivo |
|---|---|
| `pH` | 50% de missings (206/412) |
| `baby_antibiotics` | Variância zero — todos os 412 bebês = "No" |
| `baby_birthday` | Não é feature clínica; idade já capturada por `Normed Age at Collection` |
| `sample_storage` | Variável de controle experimental, sem relevância clínica |
| 550 colunas de microbioma | O cluster DMM já é uma compressão supervisionada dessas variáveis; incluí-las causaria data leakage no Modelo 2 |

---

## 6. Próximos passos

- **Sprint 2 (14/04):** encoding das variáveis categóricas, normalização, treino do Random Forest para classificação de cluster com validação cruzada estratificada, avaliação com F1-score ponderado
- **Sprint 3 (28/04):** XGBoost para predição de desfecho adverso, `scale_pos_weight` para lidar com desbalanceamento, avaliação com Recall e ROC-AUC
- **Sprint 4 (05/05):** SHAP nos dois modelos — summary plot, beeswarm, waterfall por paciente
- **Sprint 5 (12/05):** artigo no Medium, vídeo demonstrativo, slides de apresentação
