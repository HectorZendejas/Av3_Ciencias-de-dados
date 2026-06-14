# 💊 Detecção de Comprimidos em Cartelas (Blister) com YOLOv8

**Disciplina:** Ciência de Dados  
**Projeto 3 — Deep Learning com YOLO**

**GRUPO D:**
- Hector Zendejas Rebouças - 2315024

- Brenno Damiany Castro Vidal - 2315088

- Diego Henrique Santos Queiroz - 2315108

---

## 🎯 Descrição do Projeto

Este projeto treina um modelo **YOLOv8n** para detectar e classificar o estado de cada célula em cartelas de comprimidos (*blister packs*), identificando se a célula contém um comprimido (`pill`) ou está vazia (`no_pill`).

A aplicação é relevante na área da saúde: auxiliar pacientes e profissionais a monitorar o consumo correto de medicamentos de forma automatizada com visão computacional.

> As classes `pill` e `no_pill` **não existem** nas 80 categorias do dataset COCO, tornando esta uma tarefa inédita para o YOLO pré-treinado — exigindo fine-tuning supervisionado com dados específicos.

---

## 📦 Dataset

**Fonte:** [Pharma Pill Blister Inspection — Roboflow Universe](https://universe.roboflow.com/pharma-inspection-ewpw6/pharma-pill-blister-inspection/dataset/2)  
**Licença:** CC BY 4.0

| Divisão | Imagens |
|---|---|
| Treino | 1770 |
| Validação | 147 |

**Classes:** `no_pill` · `pill`

---

## 🏗️ Estrutura do Projeto

```
Projeto 3 - Deep Learning YOLO/
├── Projeto3_YOLO_Blister.ipynb          # Notebook principal
├── data_fixed.yaml                       # Configuração do dataset (gerado automaticamente)
├── yolov8n.pt                            # Pesos pré-treinados do YOLOv8n
├── pharma pill blister inspection...zip  # Dataset compactado
└── README.md                             # Este arquivo
```

---

## ▶️ Como Executar no Google Colab

### Pré-requisitos

- Conta Google com acesso ao Google Colab e Google Drive
- Arquivo `pharma pill blister inspection.v2-pills_v2_ylv8.yolov8.zip` (dataset)

### Passo a passo

**1. Preparar o Google Drive**

Faça upload do arquivo `.zip` do dataset para a **raiz do seu Google Drive** (Meu Drive), sem colocá-lo em nenhuma subpasta.

**2. Abrir o notebook no Colab**

Acesse o notebook pelo link compartilhado ou faça upload do arquivo `Projeto3_YOLO_Blister.ipynb` diretamente no [Google Colab](https://colab.research.google.com).

**3. Ativar GPU**

Antes de executar qualquer célula:
> **Ambiente de execução → Alterar tipo de ambiente de execução → T4 GPU → Salvar**

**4. Executar as células**

Execute as células na ordem. A primeira célula de configuração irá:
- Montar o Google Drive automaticamente
- Copiar o dataset do Drive para o ambiente Colab
- Descompactar o dataset

As demais células realizam, na sequência:
1. Instalação do Ultralytics
2. Configuração do dataset e geração do `data_fixed.yaml`
3. Treinamento do YOLOv8n (50 épocas com early stopping)
4. Avaliação das métricas no conjunto de validação
5. Inferência nas imagens reais capturadas pelo grupo

> ⚠️ O treinamento leva aproximadamente **15 minutos** com GPU T4.

---

## 📊 Resultados Obtidos

### Métricas de avaliação

| Métrica | Valor |
|---|---|
| **mAP@0.5** | **0.9885** |
| **mAP@0.5:0.95** | **0.798** |
| **Precision** | **0.9823** |
| **Recall** | **0.9616** |

### Métricas por classe

| Classe | Precision | Recall | mAP@0.5 |
|---|---|---|---|
| `no_pill` | 0.9740 | 0.9932 | 0.9890 |
| `pill` | 0.9906 | 0.9300 | 0.9880 |
| **Média** | **0.9823** | **0.9616** | **0.9885** |

### Curvas de treinamento

O modelo convergiu em **28 épocas** (early stopping ativado), com melhor desempenho registrado na **época 18**.

- As curvas de `train_loss` e `val_loss` decrescem juntas sem sinais de overfitting
- A curva F1 atingiu pico de **0.97** no limiar de confiança **0.502**
- A matriz de confusão mostra erros principalmente com `background`, com pouquíssima confusão entre `pill` e `no_pill`

---

## 🔍 Demonstração Visual

### Predições no conjunto de validação

O modelo detecta corretamente células com e sem comprimidos em imagens do dataset:

> *Resultados gerados automaticamente na seção 5.1 do notebook.*

### Predições em imagens reais capturadas pelo grupo

O modelo foi aplicado em fotos tiradas com celular de cartelas reais:

- Confiança majoritariamente entre **0.70 e 0.85**
- Detecção correta de comprimidos e células vazias em diferentes tipos de cartela
- Pequenos falsos positivos em bordas e embalagens externas
- Desempenho ligeiramente menor em fotos com ângulo ou reflexo, esperado dado que o dataset de treino usa imagens frontais padronizadas

> *Visualizações completas disponíveis na seção 5.2 do notebook.*

---

## 🛠️ Tecnologias Utilizadas

| Ferramenta | Versão | Função |
|---|---|---|
| Python | 3.12 | Linguagem principal |
| Ultralytics YOLOv8 | 8.4.67 | Detecção de objetos |
| PyTorch | 2.11 + CUDA | Backend de deep learning |
| Google Colab | — | Ambiente com GPU T4 |
| Roboflow | — | Dataset e anotações |
| PIL / Matplotlib | — | Visualização |

---

## ⚙️ Hiperparâmetros de Treinamento

| Parâmetro | Valor | Justificativa |
|---|---|---|
| `epochs` | 50 | Suficiente para convergência |
| `imgsz` | 640 | Padrão YOLOv8, compatível com o dataset |
| `batch` | 16 | Equilibra memória GPU e estabilidade |
| `patience` | 10 | Early stopping automático |
| `optimizer` | auto | SGD/AdamW selecionado pelo Ultralytics |

---

## 🚀 Melhorias Futuras

- Adicionar classe `half` para comprimidos partidos ou incompletos
- Expandir o dataset com cartelas de diferentes formatos e medicamentos
- Testar versões maiores do YOLOv8 (small, medium) para maior precisão
- Adaptar para inferência em tempo real via câmera de celular
- Integrar a um aplicativo móvel para monitoramento de adesão ao tratamento
