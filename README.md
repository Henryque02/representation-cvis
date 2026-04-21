# representation-cvis

implementation of classical object representation & description pipeline, with an introductory look at deep learning segmentation

## estrutura do projeto

```
representation-cvis/
├── notebooks/
│   ├── Representacao.ipynb              # parte 1: binarização, rotulação de componentes conectados, análise comparativa
│   ├── Descricao.ipynb                  # parte 2: extração de features, classificação por regras, validação manual
│   └── Introducao_Segmentacao_DL.ipynb  # parte 3: segmentação semântica e por instâncias com modelos pré-treinados
├── src/
│   └── features.py                      # funções auxiliares para extração de features (área, circularidade, solidez, etc.)
├── images/
│   ├── input/                           # imagem base de alta resolução (≥ 1280×960, 8–20 objetos)
│   └── results/                         # imagens binárias, blobs coloridos, anotações e figuras exportadas
├── pyproject.toml                       # dependências e metadados do projeto (uv)
├── uv.lock                              # lockfile das dependências
├── .python-version                      # versão de Python usada no projeto
└── README.md
```

operações morfológicas (Parte 1) são feitas nos notebooks com **OpenCV** (`cv2.morphologyEx`).
features geométricas (Parte 2) são extraídas com `cv2.moments`, `cv2.fitEllipse`, `cv2.arcLength`, etc.
segmentação com DL (Parte 3) usa modelos pré-treinados via **Transformers** (HuggingFace) e **torchvision**.

## inicializando

o setup é feito com `uv sync`:

1. instale o uv (se não tiver):

```
curl -LsSf https://astral.sh/uv/install.sh | sh
uv --version
```

2. instale as dependências e ative a venv:

```
uv sync
uv venv
```

inicializada a venv, só executar os notebooks :)

> **nota sobre a Parte 3:** os modelos de DL são baixados automaticamente pelo HuggingFace na primeira execução (~300 MB). certifique-se de ter conexão disponível.

## to-do – roteiro da atividade (por partes)

### Parte 1 – Representação

- [ ] Preparar imagem base (≥ 1280×960, 8–20 objetos bem separados sobre fundo uniforme)
- [ ] Converter para escala de cinza e aplicar **limiar global fixo** (baseado no histograma)
- [ ] Aplicar **Otsu** e **limiarização adaptativa**, exibindo os três resultados lado a lado
- [ ] Aplicar **opening** e **closing** na melhor binarização com pelo menos dois tamanhos de elemento estruturante
- [ ] Rotular componentes conectados com `cv2.connectedComponentsWithStats`
- [ ] Gerar imagem colorida com cada blob em cor distinta (colormap)
- [ ] Sobrepor contornos e bounding boxes com ID de cada componente na imagem original
- [ ] Plotar histograma de áreas dos blobs
- [ ] Comparar rotulação com **conectividade-4 vs. conectividade-8** (nº de blobs, área média, tempo)
- [ ] Discutir qual binarização produziu resultados mais fiéis à contagem real

### Parte 2 – Descrição

- [ ] Extrair features de cada blob e armazenar em um **DataFrame** (pandas):
  - área, centro de massa, bounding box, eixos da elipse, orientação, perímetro, circularidade, solidez
- [ ] Classificar blobs por regras (ex.: pequeno/médio/grande, circular/irregular, compacto/com concavidades)
- [ ] Anotar classe de cada blob diretamente na imagem (texto + cor por classe)
- [ ] Gerar **scatter plot** de Área × Circularidade colorido por classe
- [ ] Validar manualmente 4–5 objetos (medir área e perímetro com editor de imagem)
- [ ] Apresentar erro percentual em tabela e discutir fontes de discrepância
- [ ] *(opcional)* Comparar blobs com Hu Moments (`cv2.matchShapes`)
- [ ] *(opcional)* Filtrar blobs que tocam a borda ou abaixo de área mínima
- [ ] *(opcional)* Plotar assinaturas radiais de contorno de dois objetos

### Parte 3 – Introdução à Segmentação com DL

- [ ] Escolher imagem realista com classes comuns (pessoa, carro, cachorro, cadeira, etc.)
- [ ] Executar **segmentação semântica** com modelo pré-treinado (SegFormer via HuggingFace)
- [ ] Exibir: imagem original → mapa de segmentação com legenda → sobreposição sobre original
- [ ] Executar **segmentação por instâncias** com modelo pré-treinado (Mask R-CNN via torchvision)
- [ ] Exibir máscaras individuais por objeto, contornos e separação entre instâncias da mesma classe
- [ ] Comparar visualmente segmentação semântica vs. por instâncias
- [ ] Discutir: diferenças em relação ao pipeline clássico das Partes 1 e 2, classes bem/mal reconhecidas, limitações observadas

### Entrega e apresentação

- [ ] `Representacao.ipynb`: código comentado, análise quantitativa, tabela comparativa, tempo de execução
- [ ] `Descricao.ipynb`: DataFrame com features, scatter plot, tabela de validação, imagens anotadas
- [ ] `Introducao_Segmentacao_DL.ipynb`: código simples, imagens de entrada/saída, explicação conceitual
- [ ] 3 apresentações `.pptx` correspondentes

## como colaborar?

1. atualize sua branch `main` local:
   - `git checkout main`
   - `git pull origin main`

2. crie uma nova branch para sua tarefa:
   - para nova funcionalidade: `git checkout -b feature/nome-da-funcionalidade`
   - para correção de bug: `git checkout -b fix/descricao-do-bug`

3. faça as alterações e teste localmente (notebooks, scripts em `src/`, etc.).

4. adicione e faça commit das mudanças:
   - `git add .`
   - `git commit -m "tipo: descrição curta e clara da mudança"`

5. envie a branch para o repositório remoto:
   - `git push origin feature/nome-da-funcionalidade`

6. abra um Pull Request (PR) para `main` descrevendo:
   - **o que** foi alterado
   - **por que** foi alterado
   - como **reproduzir/testar** os resultados

7. aguarde revisão, ajuste se necessário e, após aprovação, faça o merge.
