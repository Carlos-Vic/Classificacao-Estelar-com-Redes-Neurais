# Classificação Estelar com Redes Neurais — SDSS17

> README gerado com o auxílio do [Claude](https://claude.ai) (Anthropic)

Classificação de estrelas, galáxias e quasares a partir de dados espectrais do Sloan Digital Sky Survey (SDSS17) utilizando redes neurais com PyTorch.

📓 **Notebook no Kaggle:** [classificacao-estelar-com-redes-neurais](https://www.kaggle.com/code/carlosvictor221/classifica-o-estelar-com-redes-neurais)

---

## Sobre o Dataset

100.000 observações do SDSS com 17 features e 1 coluna target com 3 classes:

| Classe | Quantidade |
|---|---|
| GALAXY | 59.445 |
| STAR | 21.594 |
| QSO | 18.961 |

Features utilizadas após análise exploratória: `u`, `g`, `r`, `i`, `z`, `redshift` — os filtros fotométricos e o redshift são as medidas físicas mais relevantes para a classificação.

---

## Estrutura do Projeto

```
1. Análise Exploratória
   ├── Identificação de ruído (412 entradas GALAXY com redshift=0)
   ├── Análise do desbalanceamento de classes
   └── Seleção de features via matriz de correlação

2. Pré-processamento
   ├── Remoção de ruído
   ├── Train/Val/Test split (70/15/15)
   ├── StandardScaler (fit apenas no treino)
   └── Class weights para o desbalanceamento

3. Experimentos
   ├── Modelo base (6→64→3)
   ├── Variação de profundidade e largura
   ├── Variação de learning rate e épocas
   └── Regularização e otimizadores

4. Avaliação Final no conjunto de teste
```

---

## Experimentos e Resultados

### Arquitetura

| Modelo | Val Loss | Acurácia |
|---|---|---|
| Base (6→64→3) | ~0.092 | ~97.0% |
| Profundo+Largo (6→256→128→64→3) | ~0.081 | ~97.35% |

### Learning Rate (50 épocas, arquitetura base)

| LR | Comportamento | Val Loss | Acurácia |
|---|---|---|---|
| 1e-1 | Instável, picos no val loss | ~0.106 | ~96.8% |
| 1e-2 | Convergência rápida com oscilação | ~0.089 | ~97.3% |
| 1e-3 | Melhor — suave e estável | ~0.092 | ~97.0% |
| 1e-4 | Underfitting, aprendizado lento demais | 0.127 | 95.7% |

### Épocas (LR=1e-3, arquitetura base)

| Épocas | Val Loss | Acurácia |
|---|---|---|
| 10 | 0.1126 | 95.7% |
| 50 | ~0.092 | ~97.0% |
| 200 | 0.0840 | 96.97% |

### Regularização e Otimizadores (Dropout p=0.3, modelo profundo)

| Otimizador | Val Loss | Acurácia |
|---|---|---|
| Adam | 0.0944 | 96.38% |
| AdamW | 0.0867 | 96.94% |
| SGD | 0.0977 | 96.63% |

---

## Resultado Final

Modelo: `6→256→128→64→3` com Dropout (p=0.3) e AdamW (lr=1e-3, weight_decay=1e-4)

**Acurácia no teste: 97%**

| Classe | Precision | Recall | F1-Score |
|---|---|---|---|
| GALAXY | 0.98 | 0.97 | 0.98 |
| STAR | 0.98 | 1.00 | 0.99 |
| QSO | 0.93 | 0.95 | 0.94 |

STAR foi classificada com perfeição (3239/3239). QSO foi a mais difícil — quasares são núcleos galácticos ativos com espectros similares aos de galáxias, o que explica os 154 casos confundidos.

---

## Tecnologias

- Python 3
- PyTorch
- scikit-learn
- pandas / numpy
- matplotlib / seaborn
