# Classificador de Dígitos Manuscritos — MNIST

Projeto de Machine Learning desenvolvido para classificação de dígitos manuscritos utilizando o dataset MNIST.

O projeto abrange análise exploratória, pré-processamento, treinamento e comparação de diferentes algoritmos de classificação, análise de erros, avaliação de robustez a dados fora da distribuição (OOD) e testes com imagens manuscritas produzidas fora do dataset original.

## Objetivo

Desenvolver e avaliar modelos capazes de identificar dígitos manuscritos de 0 a 9 a partir de imagens de 28 × 28 pixels.

Foram avaliados três algoritmos:

- K-Nearest Neighbors (KNN)
- Random Forest
- Multi-Layer Perceptron (MLP)

Além da avaliação tradicional, o projeto investiga o comportamento do modelo diante de classes não apresentadas durante o treinamento e de imagens manuscritas externas ao MNIST.

## Dataset

Foi utilizado o dataset MNIST disponibilizado por meio do OpenML.

O conjunto possui:

- 70.000 imagens;
- 10 classes (dígitos de 0 a 9);
- imagens em escala de cinza;
- resolução de 28 × 28 pixels;
- 784 atributos por imagem.

Os dados foram divididos de forma estratificada em:

| Conjunto | Amostras | Percentual |
|---|---:|---:|
| Treino | 49.000 | 70% |
| Validação | 7.000 | 10% |
| Teste | 14.000 | 20% |

Os pixels foram convertidos para `float32` e normalizados do intervalo 0–255 para 0–1.

## Modelos

### KNN

Foram avaliadas diferentes combinações de:

- `n_neighbors`: 3, 5 e 7;
- `weights`: `uniform` e `distance`.

Melhor configuração:

```text
n_neighbors = 3
weights = distance
```

### Random Forest

Foram avaliadas combinações de:

- `n_estimators`: 100 e 200;
- `max_depth`: 15, 25 e sem limite.

Melhor configuração:

```text
n_estimators = 200
max_depth = None
```

### MLP

Foram avaliadas três arquiteturas:

```text
(50,)
(100,)
(100, 50)
```

com duas funções de ativação:

```text
relu
tanh
```

A melhor configuração encontrada foi:

```text
hidden_layer_sizes = (100,)
activation = relu
max_iter = 200
random_state = 42
```

O modelo convergiu antes do limite máximo de iterações.

## Resultados

Os modelos foram avaliados no conjunto de teste utilizando Accuracy, Precision, Recall e F1-score ponderados.

| Modelo | Accuracy | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|
| MLP | 97,77% | 97,77% | 97,77% | 97,77% |
| KNN | 97,21% | 97,24% | 97,21% | 97,21% |
| Random Forest | 96,66% | 96,66% | 96,66% | 96,66% |

A MLP apresentou o melhor desempenho geral entre os modelos avaliados.

Além das métricas, foram analisados os tempos de treinamento e predição e as matrizes de confusão de cada modelo.

## Análise de erros

A análise das matrizes de confusão mostrou que alguns dígitos apresentam maior dificuldade de classificação.

O par de maior confusão identificado nos três modelos foi:

```text
4 → 9
```

Quantidade de ocorrências:

| Modelo | Erros 4 → 9 |
|---|---:|
| KNN | 35 |
| Random Forest | 33 |
| MLP | 23 |

A MLP apresentou menos ocorrências dessa confusão.

## Experimento OOD

Para avaliar o comportamento do modelo diante de dados fora da distribuição de treinamento, foi realizado um experimento ocultando completamente as classes **4 e 9** durante o treinamento de uma nova MLP.

O modelo foi treinado apenas com:

```text
0, 1, 2, 3, 5, 6, 7 e 8
```

Posteriormente, foram apresentadas exclusivamente imagens das classes 4 e 9.

Como essas classes não existiam no espaço de saída aprendido, o modelo foi obrigado a atribuí-las a alguma das classes conhecidas.

A análise das probabilidades revelou um comportamento importante: mesmo diante de classes nunca vistas durante o treinamento, o modelo frequentemente realizou classificações com elevada confiança.

Entre as imagens OOD:

- 79,43% receberam probabilidade máxima ≥ 80%;
- 69,81% receberam probabilidade máxima ≥ 90%;
- 61,54% receberam probabilidade máxima ≥ 95%;
- 46,70% receberam probabilidade máxima ≥ 99%.

O experimento demonstra que uma alta probabilidade de classificação não significa necessariamente que o modelo reconheça quando está diante de uma entrada desconhecida.

## Teste com imagens manuscritas externas

Também foram produzidas imagens manuscritas próprias, independentes do dataset MNIST.

O pipeline desenvolvido com OpenCV realiza:

1. carregamento da imagem;
2. conversão para escala de cinza;
3. inversão das cores quando necessária;
4. detecção da região do dígito;
5. recorte;
6. redimensionamento preservando a proporção;
7. centralização em uma imagem 28 × 28;
8. centralização da massa do dígito;
9. normalização para 0–1;
10. transformação para 784 atributos;
11. classificação com a MLP.

Foram avaliadas 20 imagens externas, contendo dois exemplos de cada dígito entre 0 e 9.

O modelo classificou corretamente as 20 imagens neste conjunto experimental.

Esse resultado deve ser interpretado considerando o tamanho reduzido e as condições controladas do conjunto externo.

## Demonstração interativa

O projeto também possui um notebook de demonstração que permite selecionar uma imagem de um dígito manuscrito e executar o pipeline completo de classificação.

A demonstração apresenta:

- imagem processada;
- classe prevista;
- probabilidade da classe selecionada;
- distribuição das probabilidades entre os dígitos de 0 a 9.

## Estrutura do projeto

```text
classificador_de_digitos/
├── data/
│   ├── raw/
│   └── external/
│       └── handwritten/
├── models/
│   └── mlp_mnist.joblib
├── notebooks/
│   ├── classificador_de_digitos.ipynb
│   └── classificador_de_digitos_demo.ipynb
├── requirements.txt
├── .gitignore
└── README.md
```

## Tecnologias utilizadas

- Python
- NumPy
- Pandas
- Matplotlib
- Seaborn
- Scikit-learn
- OpenCV
- Joblib
- Jupyter Notebook
- ipywidgets

## Instalação

Clone o repositório e acesse a pasta do projeto:

```bash
git clone <URL_DO_REPOSITORIO>
cd classificador_de_digitos
```

Crie um ambiente virtual:

```bash
python -m venv .venv
```

Instale as dependências:

```bash
pip install -r requirements.txt
```

Depois, execute o notebook principal:

```text
notebooks/classificador_de_digitos.ipynb
```

## Conclusão

O projeto demonstrou o desenvolvimento de um pipeline completo de classificação de dígitos manuscritos, desde a exploração e preparação dos dados até a comparação de modelos e avaliação com dados externos.

Entre os algoritmos avaliados, a MLP apresentou o melhor desempenho, atingindo aproximadamente 97,8% de acurácia no conjunto de teste.

Os experimentos OOD também evidenciaram uma limitação importante: modelos classificadores podem apresentar elevada confiança mesmo diante de classes desconhecidas. Dessa forma, além das métricas tradicionais, a análise de erros, robustez e comportamento diante de dados externos é fundamental para compreender as limitações de uma solução de Machine Learning.

## Funcionalidade bônus — classificação de imagem local

Como recurso adicional, o projeto permite utilizar o modelo MLP para classificar uma única imagem armazenada localmente no computador.

A funcionalidade abre uma janela para seleção de um arquivo nos formatos `.png`, `.jpg` ou `.jpeg`. Depois da seleção, a imagem passa pelo mesmo pipeline de pré-processamento aplicado às imagens externas:

1. conversão para escala de cinza;
2. ajuste do fundo e inversão das cores, quando necessária;
3. binarização da imagem;
4. identificação da região ocupada pelo dígito;
5. recorte e remoção do fundo;
6. redimensionamento proporcional;
7. inserção em uma matriz de 28 × 28 pixels;
8. centralização pelo centro de massa;
9. normalização dos pixels para o intervalo entre 0 e 1;
10. transformação da matriz em um vetor de 784 atributos;
11. classificação pelo modelo MLP.

Ao final, são apresentados:

- a imagem original selecionada;
- a imagem processada no formato 28 × 28;
- o dígito previsto;
- a probabilidade máxima da previsão;
- um gráfico com as probabilidades atribuídas às classes de 0 a 9.

A funcionalidade pode ser executada rodando as ultimas celulas do notebook.