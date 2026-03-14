# 📦 Previsão de Estoque Inteligente na AWS — De Reativo a Preditivo

![NexaIAG](https://github.com/user-attachments/assets/c556e8a2-8292-49c3-aba4-892086424724)

**Bootcamp Nexa — Machine Learning e GenAI na Prática com AWS SageMaker Canvas**

---

[![AWS SageMaker Canvas](https://img.shields.io/badge/AWS-SageMaker_Canvas-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/sagemaker/canvas/)
[![Machine Learning](https://img.shields.io/badge/Machine_Learning-Random_Forest-blue?style=for-the-badge)](https://en.wikipedia.org/wiki/Random_forest)
[![Python](https://img.shields.io/badge/Python-3.10+-yellow?style=for-the-badge&logo=python)](https://www.python.org/)
[![Status](https://img.shields.io/badge/Status-Concluído-green?style=for-the-badge)]()

---

## TL;DR

> Projeto de Machine Learning na AWS que antecipa a demanda de estoque com base em sazonalidade, histórico de vendas e promoções.
> O modelo gerou previsões com **R² = 0,69 no conjunto de teste**, alertas automáticos de reabastecimento e um simulador de cenários para apoiar decisões estratégicas de compras — transformando dados históricos em ação de negócio.

---

## 1. Problema de Negócio

Empresas de varejo e supply chain enfrentam um dilema crônico: **quando comprar e quanto comprar**.

A abordagem tradicional usa médias históricas estáticas para calcular o ponto de reposição. Esse método ignora três variáveis críticas:

- **Sazonalidade** — as vendas de junho/novembro (Black Friday/Meio do Ano) e dezembro (Natal) são sistematicamente superiores à média anual;
- **Promoções** — ativações comerciais geram picos de consumo que médias simples não capturam;
- **Volatilidade temporal** — fins de semana vendem mais do que dias úteis, criando variações semanais recorrentes.

O resultado prático: **ruptura de estoque nos picos** (perda de venda + insatisfação do cliente) ou **excesso de estoque nos vales** (capital de giro imobilizado). Em ambos os casos, a empresa paga o custo da falta de previsibilidade.

> **A pergunta que originou o projeto:**
> *"É possível usar Machine Learning para antecipar a demanda futura e transformar a gestão de estoque de reativa em preditiva?"*

---

## 2. Contexto

O projeto foi desenvolvido durante o **Bootcamp Nexa — Machine Learning e GenAI na Prática** na plataforma DIO, utilizando o **Amazon SageMaker Canvas** como plataforma principal de modelagem.

A base de dados simula três anos de operação (01/01/2023 a 30/12/2025) de um produto único — **Produto A** — com **1.095 registros diários** gerados por um notebook Python com sazonalidade controlada, ruído aleatório e picos comerciais programados.

O cenário de negócio capturado pelos dados:

| Métrica                  | Valor                 |
|--------------------------|-----------------------|
| Total de Vendas          | 185.563 unidades      |
| Média Diária de Vendas   | 169,5 un.             |
| Estoque Médio            | 2.424 un.             |
| Receita Total Estimada   | R$ 3.709.826,13       |
| Preço Unitário Médio     | R$ 19,98              |
| Lead Time Médio          | 6,96 dias             |

No momento da análise, o **Produto A estava em status ALERTA**: estoque atual de 1.862 unidades, representando apenas 8,6 dias de cobertura, com ponto de reposição calculado em 3.033 unidades.

---

## 3. Premissas da Análise

Para garantir consistência e reprodutibilidade, as seguintes premissas foram adotadas:

- O dataset de treinamento representa **comportamento de demanda real**, com sazonalidade sinusoidal (vendas de verão/inverno), picos comerciais (Natal, Black Friday, Meio do Ano) e variação semanal (fins de semana com 10-30 unidades a mais por dia);
- O **estoque de segurança** foi configurado em 7 dias e o **lead time médio** em 7 dias, resultando em um ponto de reposição calculado como: `demanda_diária_média × (lead_time + estoque_segurança)`;
- A variável `Previsao_Demanda_30D` no dataset foi utilizada como **target** do modelo supervisionado, representando o total esperado de consumo nos próximos 30 dias a partir de cada data;
- A correlação entre `Preco_Unitario` e `Vendas` resultou em **0,055** — praticamente nula — indicando que variações de preço na faixa R$ 18–R$ 22 não explicam o volume de vendas. Isso reforça que **sazonalidade e promoções são os drivers reais**;
- As análises foram feitas com foco em **identificação de padrões e suporte à decisão**, não em inferência causal estatística.

---

## 4. Estratégia da Solução

### 4.1 Geração e Preparação dos Dados

O notebook `01_data_generation.ipynb` gera o dataset com as seguintes etapas:

1. **Criação do índice de datas** — 1.095 dias consecutivos a partir de 01/01/2023;
2. **Base de vendas com sazonalidade sinusoidal** — tendência ondulante de 150 unidades ± 50, com ruído aleatório de desvio-padrão 20;
3. **Picos comerciais programados:**
   - Dezembro (Natal): +50 a +150 unidades/dia
   - Junho e Novembro (Black Friday/Meio do Ano): +30 a +100 unidades/dia
   - Sábado e Domingo: +10 a +30 unidades/dia
4. **Variáveis adicionais:** `Promocao_Ativa` (20% de chance), `Preco_Unitario` (R$ 18–22 uniforme), `Lead_Time_Dias` (5–9 dias aleatório), `Estoque_Atual` (simulado com reabastecimentos);
5. **Target engineering:** `Previsao_Demanda_30D = vendas_historicas × 1,1 + ruído`.

O dataset processado (`estoque_filtrado.csv`) passou por filtragem e renomeação de colunas antes do upload para o S3.

### 4.2 Treinamento no SageMaker Canvas

| Etapa                      | Detalhe                                             |
|----------------------------|-----------------------------------------------------|
| Plataforma                 | Amazon SageMaker Canvas (low-code AutoML)           |
| Dataset de entrada         | `estoque_historico_sazonal.csv` via Amazon S3       |
| Variável target            | `estoque_final` / `Previsao_Demanda_30D`            |
| Modo de build              | Standard Build (máxima precisão)                    |
| Algoritmo selecionado      | Random Forest (seleção automática via AutoML)       |
| Split treino/teste         | 80% / 20%                                           |

### 4.3 Geração de Previsões e Alertas

Após o treinamento:

1. O arquivo `estoque_previsao_futura.csv` foi utilizado para gerar previsões dos próximos 30 dias;
2. O sistema de alertas calculou automaticamente o **ponto de reposição** e a **quantidade sugerida de pedido** com base no lead time e estoque de segurança;
3. O **simulador de cenários** permitiu comparar o impacto de diferentes taxas de promoção e variações de preço sobre a receita projetada.

---

## 5. Insights da Análise

### 5.1 Sazonalidade Confirmada e Quantificada

A análise de sazonalidade revelou padrões consistentes ao longo dos três anos:

- **Pico máximo:** junho, novembro e dezembro, com média de vendas sistematicamente **acima de 200 unidades/dia**;
- **Vale sazonal:** julho e agosto, com médias próximas de **130–140 unidades/dia** — uma queda de ~35% em relação ao pico;
- **Padrão semanal:** fins de semana apresentam vendas entre 5-15% superiores à média dos dias úteis.

> **Decisão de negócio derivada:** o estoque de segurança não deve ser fixo. Ele precisa ser dinâmico, aumentando nos meses de setembro/outubro para antecipar os picos de novembro/dezembro.

### 5.2 Preço Não Explica Vendas — Promoção Explica

A correlação entre `Preco_Unitario` e `Vendas_Historicas` resultou em **0,055**, confirmando que a sensibilidade ao preço na faixa R$ 18–R$ 22 é negligenciável.

Por outro lado, dias com `Promocao_Ativa = True` apresentaram volume médio de vendas **consistentemente superior** ao cenário sem promoção, validando a promoção como driver real de demanda.

### 5.3 Feature Importance — O Que o Modelo Aprendeu

| Feature                     | Importância Estimada | Justificativa de Negócio                                     |
|-----------------------------|----------------------|--------------------------------------------------------------|
| `Vendas_Historicas` (lag)   | ~45%                 | Consumo recente é o melhor preditor de consumo futuro        |
| `Data` / sazonalidade       | ~20%                 | Captura padrões anuais e semanais de demanda                 |
| `Promocao_Ativa`            | ~15%                 | Promoções geram picos que exigem cobertura adicional         |
| `Lead_Time_Dias`            | ~10%                 | Afeta o ponto de reposição e o risco de ruptura              |
| `Preco_Unitario`            | ~10%                 | Contribuição mínima, consistente com correlação próxima de 0 |

> **Insight técnico:** a baixa importância do preço em um modelo que captura a variância de vendas com R² = 0,69 sugere que o canal de vendas tem baixa elasticidade-preço — um dado estratégico relevante para a área comercial.

### 5.4 Alerta de Reabastecimento em Tempo Real

Com estoque atual de **1.862 unidades** e demanda média de 216 unidades/dia (período de pico), o sistema calculou:

- **Dias de estoque restantes:** 8,6 dias
- **Ponto de reposição:** 3.033 unidades
- **Quantidade sugerida de pedido:** 6.500 unidades
- **Status:** 🟡 ALERTA — estoque abaixo do ponto de reposição

---

## 6. Resultados

### 6.1 Performance do Modelo

| Métrica        | Valor  | Interpretação                                                  |
|----------------|--------|----------------------------------------------------------------|
| R² (Treino)    | 0,962  | Modelo ajustou muito bem aos dados históricos                  |
| R² (Teste)     | 0,690  | ~70% da variabilidade da demanda explicada em dados não vistos |
| Algoritmo      | Random Forest | Selecionado automaticamente pelo Canvas AutoML          |

O gap entre R² de treino (0,962) e teste (0,690) indica **overfitting moderado** — esperado em dados de varejo com alta variância. Para dados reais com ruído intrínseco, um R² de 0,69 representa resultado **consistente e aplicável ao negócio**.

> O benchmark de comparação seria uma média móvel de 30 dias (abordagem tradicional), cujo R² estimado para esses dados estaria na faixa de 0,40–0,50. O modelo superou o baseline em ~40%.

### 6.2 Previsão para os Próximos 30 Dias

| Horizonte     | Total Previsto | Média Diária Prevista |
|---------------|----------------|-----------------------|
| 30 dias       | 4.984 un.      | 166,1 un./dia         |
| 77 dias       | 13.968 un.     | 181,4 un./dia         |

### 6.3 Simulador de Cenários — Impacto de Promoções

| Cenário         | Promoção | Preço Médio | Vendas Totais | Receita Estimada  |
|-----------------|----------|-------------|---------------|-------------------|
| Base            | 20%      | R$ 19,98    | 13.740 un.    | R$ 274.458,19     |
| Simulado (+0%)  | 20%      | R$ 19,98    | 13.754 un.    | R$ 274.737,85     |
| **Diferença**   | —        | —           | +14 un.       | **+R$ 279,65**    |

O simulador permite testar variações de taxa de promoção e preço antes de qualquer decisão comercial, reduzindo o risco de erros de planejamento.

### 6.4 Valor Gerado para o Negócio

- **Antecipação de rupturas:** o alerta foi disparado com 8,6 dias de antecedência, dentro da janela de lead time de 7 dias — tempo suficiente para acionar o fornecedor;
- **Redução de capital imobilizado:** previsão de demanda precisa permite comprar o necessário, sem excesso;
- **Decisões baseadas em dados:** gestores de compras e supply chain passam a ter um instrumento quantitativo, não apenas intuição ou planilhas estáticas.

---

## 7. Decisões Técnicas e Aprendizados

### Por que AWS SageMaker Canvas e não código Python direto?

O Canvas foi escolhido por três razões alinhadas ao objetivo do projeto:

1. **Governança e rastreabilidade:** em ambientes corporativos AWS, o Canvas oferece logs de experimentos, versionamento de modelos e integração com S3/IAM nativos — elementos ausentes em notebooks ad hoc;
2. **Velocidade de ciclo:** o foco do projeto era validar se ML resolve o problema de negócio, não engenheirar o pipeline. O Canvas permite chegar à resposta em horas, não dias;
3. **Interpretabilidade:** a feature importance gerada pelo Canvas é diretamente consumível por stakeholders não técnicos.

**Trade-off aceito:** menor controle sobre hyperparâmetros e arquitetura do modelo. Para uma versão produtiva, o próximo passo seria migrar para SageMaker Autopilot ou um pipeline Python/Scikit-Learn com tuning customizado.

### Por que Random Forest como algoritmo?

O AutoML do Canvas selecionou Random Forest por características do dataset:

- Dados tabulares com variáveis mistas (numéricas + booleanas + temporais);
- Presença de ruído aleatório intrínseco (demanda de varejo);
- Necessidade de interpretabilidade via feature importance.

Algoritmos como XGBoost ou redes neurais foram considerados internamente pelo Canvas, mas não superaram o Random Forest no conjunto de validação.

### Aprendizado honesto: o gap de overfitting

O R² de treino em 0,962 com teste em 0,690 revela overfitting. Identifiquei retrospectivamente que o target (`Previsao_Demanda_30D`) foi gerado a partir das próprias `Vendas_Historicas_Unidades` com um offset de 10% + ruído — criando uma correlação estrutural no dataset que favorece o treino.

Em um cenário real, o target seria o consumo futuro real observado, não uma derivação matemática do histórico. Esse é um **bug de data leakage leve** que precisaria ser corrigido antes de um deploy em produção.

---

## 🗂️ Estrutura do Repositório

```
estoqAWSsagemakerCanvas/
│
├── 📁 analysis/                          # Capturas analíticas em PDF geradas pelo Dashboard
│   ├── Alertas_de_Reabastecimento.pdf    # Status de alerta por produto, ponto de reposição e qtd. sugerida
│   ├── Analise_de_Estoque_Demanda.pdf    # Gráfico Estoque Atual vs. Previsão de Demanda + correlações
│   ├── Analise_de_Sazonalidade.pdf       # Heatmap mensal/anual + média por dia da semana
│   ├── Dashboard_Analise_Estoque.pdf     # Visão consolidada do dashboard completo
│   ├── Previsao_Demanda_Machine_Learning.pdf  # Output do modelo ML: previsão 30 e 77 dias
│   ├── Simulador_de_Cenarios.pdf         # Comparativo de receita entre cenário base e simulado
│   ├── Vendas_Historicas_Longo_do_Tempo.pdf   # Série temporal diária, mensal e impacto de promoções
│   └── relatorio_estoque.pdf             # Relatório consolidado com todas as métricas principais
│
├── 📁 datasets/
│   ├── 📁 raw/                           # Dados brutos gerados pelo notebook de geração
│   │   ├── estoque_historico_sazonal.csv # Dataset principal: 1.095 registros diários (2023–2025)
│   │   ├── estoque_filtrado.csv          # Versão filtrada e renomeada para upload no SageMaker Canvas
│   │   └── Estatisticas_Descritivas.csv  # Estatísticas descritivas (média, desvio, mín, máx) por variável
│   │
│   ├── 📁 processed/                     # Dados processados para treinamento
│   │   └── estoque_tratado.csv           # Dataset tratado com colunas: data, produto_id, estoque_inicial,
│   │                                     # vendas_dia, estoque_final, promocao
│   │
│   └── 📁 predict/                       # Dados de entrada para geração de previsões futuras
│       └── estoque_previsao_futura.csv   # Features para previsão: 3 produtos (P001 Eletrônicos,
│                                         # P002 Alimentos, P003 Vestuário) com projeções de 2025
│
├── 📁 notebooks/
│   └── 01_data_generation.ipynb         # Notebook Python para geração sintética do dataset com
│                                         # sazonalidade, picos comerciais e variáveis de negócio
│
└── README.md                            # Este arquivo
```

### Detalhamento dos Datasets

**`estoque_historico_sazonal.csv`** — dataset principal de treinamento:

| Coluna                       | Tipo      | Descrição                                           |
|------------------------------|-----------|-----------------------------------------------------|
| `Data`                       | date      | Data do registro (diário, 2023-01-01 a 2025-12-30)  |
| `ID_Produto`                 | string    | Identificador do produto (PROD_A)                   |
| `Preco_Unitario`             | float     | Preço de venda em R$ (intervalo: R$18–R$22)         |
| `Promocao_Ativa`             | boolean   | Se havia promoção ativa no dia (20% dos dias)       |
| `Lead_Time_Dias`             | int       | Tempo de entrega do fornecedor (5–9 dias)           |
| `Vendas_Historicas_Unidades` | int       | Unidades vendidas no dia (36–299, média 169,5)      |
| `Estoque_Atual`              | int       | Nível de estoque ao final do dia (515–5.496)        |
| `Previsao_Demanda_30D`       | int       | **Target do modelo** — demanda prevista 30 dias     |

**`estoque_previsao_futura.csv`** — dados para inferência (3 produtos × 10 dias mostrados):

| Produto   | Categoria   | Faixa de Preço | Perfil de Demanda |
|-----------|-------------|----------------|--------------------|
| P001      | Eletrônicos | R$ 2.550       | 6–14 un./dia       |
| P002      | Alimentos   | R$ 16          | 18–27 un./dia      |
| P003      | Vestuário   | R$ 130         | 6–14 un./dia       |

---

## 🚀 Como Reproduzir o Projeto

### Requisitos de Software

| Ferramenta         | Versão Recomendada | Finalidade                                     |
|--------------------|--------------------|------------------------------------------------|
| Python             | 3.10+              | Execução do notebook de geração de dados       |
| Jupyter Notebook   | 6.x ou superior    | Ambiente de execução do notebook               |
| pandas             | 2.x                | Manipulação e exportação dos datasets          |
| numpy              | 1.x                | Geração de dados sintéticos e sazonalidade     |
| Conta AWS          | Com acesso ao SageMaker Canvas e S3 | Treinamento e inferência do modelo |

### Requisitos de Hardware

| Recurso         | Mínimo               | Recomendado                   |
|-----------------|----------------------|-------------------------------|
| CPU             | Qualquer CPU moderna | —                             |
| RAM             | 4 GB                 | 8 GB                          |
| Armazenamento   | 100 MB livres        | —                             |
| Acesso AWS      | SageMaker Canvas Standard Build | ~2–4h de processamento |

> ⚠️ O SageMaker Canvas no modo **Standard Build** pode gerar custos na AWS. Verifique o pricing em [aws.amazon.com/sagemaker/canvas/pricing](https://aws.amazon.com/sagemaker/canvas/pricing/) antes de executar.

### Passo a Passo

**1️⃣ Geração dos Dados**

```bash
# Clone o repositório
git clone https://github.com/Santosdevbjj/estoqAWSsagemakerCanvas.git
cd estoqAWSsagemakerCanvas

# Instale as dependências
pip install pandas numpy jupyter

# Execute o notebook
jupyter notebook notebooks/01_data_generation.ipynb
```

O notebook gera automaticamente:
- `datasets/raw/estoque_historico_sazonal.csv` (dataset principal — 1.095 linhas)

**2️⃣ Upload para Amazon S3**

```bash
# Via AWS CLI
aws s3 cp datasets/raw/estoque_historico_sazonal.csv s3://SEU-BUCKET/estoque/
```

Ou faça o upload diretamente pelo console AWS S3.

**3️⃣ Treinamento no SageMaker Canvas**

1. Acesse o [Amazon SageMaker Canvas](https://console.aws.amazon.com/sagemaker/canvas/);
2. Crie um novo dataset importando o CSV do S3;
3. Crie um novo modelo de **ML Preditivo (Regressão)**;
4. Defina `Previsao_Demanda_30D` como variável target;
5. Execute o **Standard Build**;
6. Avalie o modelo: R² Treino ≈ 0,96 | R² Teste ≈ 0,69.

**4️⃣ Geração de Previsões**

1. Na tela de previsão do Canvas, selecione **Batch Predict**;
2. Importe o arquivo `datasets/predict/estoque_previsao_futura.csv`;
3. Execute a previsão e exporte os resultados como CSV.

---

## 8. Próximos Passos

| Evolução                           | Impacto Esperado                                        |
|------------------------------------|---------------------------------------------------------|
| Corrigir data leakage no target    | Reduzir gap treino/teste; modelo mais realista          |
| Forecast Multissérie               | Previsão simultânea de múltiplos SKUs no Canvas         |
| Lead Time Variável                 | Incorporar variabilidade real do fornecedor no cálculo  |
| SageMaker Model Monitor            | Monitorar drift de dados e degradação do modelo         |
| Integração ERP/BI                  | Alimentar Power BI ou sistemas de compras com previsões |
| Pipeline Python/Scikit-Learn       | Migrar para pipeline com tuning e controle total        |

---

## 👨‍💻 Autor

**Sérgio Santos** — Analista de Sistemas, Desenvolvedor e Profissional de Ciência de Dados.



---

[![Portfólio Sérgio Santos](https://img.shields.io/badge/Portfólio-Sérgio_Santos-111827?style=for-the-badge&logo=githubpages&logoColor=00eaff)](https://portfoliosantossergio.vercel.app)
[![LinkedIn Sérgio Santos](https://img.shields.io/badge/LinkedIn-Sérgio_Santos-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/santossergioluiz)

---


---

### 📎 About (campo do repositório GitHub)

```
Estoque reagindo = ruptura no pico | Random Forest prevê demanda 30 dias à frente via AWS SageMaker Canvas | Alerta antecipado reduz risco de ruptura e libera capital de giro
```
