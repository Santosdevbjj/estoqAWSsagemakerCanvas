### Previsão de Estoque Inteligente na AWS com Sagemaker Canvas

![NexaIAG](https://github.com/user-attachments/assets/c556e8a2-8292-49c3-aba4-892086424724)


**Bootcamp Nexa - Machine Learning e GenAI na Prática.**

---

[![AWS Certified](https://img.shields.io/badge/AWS-SageMaker_Canvas-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/sagemaker/canvas/)
[![Machine Learning](https://img.shields.io/badge/Machine_Learning-Regressão-blue?style=for-the-badge)](https://en.wikipedia.org/wiki/Regression_analysis)
[![Status](https://img.shields.io/badge/Status-Pronto_para_Deploy-green?style=for-the-badge)]()




---


📦 **Previsão de Estoque Inteligente na AWS — De Reativo a Preditivo**

TL;DR

> Projeto de Machine Learning na AWS que antecipa a demanda de estoque com base em sazonalidade, histórico de vendas e promoções, gerando previsões confiáveis e alertas de reabastecimento para apoiar decisões estratégicas de compras, reduzir rupturas e otimizar capital de giro.




---

💡 **1. Visão Geral do Projeto**

Este projeto aborda um problema clássico de logística e supply chain: como manter níveis de estoque adequados em um cenário de demanda volátil.

Em abordagens tradicionais, empresas utilizam médias históricas estáticas, que desconsideram fatores críticos como sazonalidade, promoções e variações temporais do consumo. 

Aqui, adotamos uma abordagem preditiva, utilizando o Amazon SageMaker Canvas, para antecipar a demanda futura e apoiar decisões de reabastecimento de forma orientada por dados.

O foco não é apenas gerar previsões, mas transformar Machine Learning em ação de negócio.


---

🎯 **2. Objetivos do Projeto**

Previsão de Demanda: antecipar o comportamento do consumo para os próximos 30 dias com base em mais de 2 anos de histórico diário.

Identificação de Sazonalidade: capturar padrões semanais e anuais de vendas.

Apoio à Decisão: fornecer subsídios para definição de pontos de reposição e planejamento de compras.



---

⚙️ **3. Decisões Técnicas & Stack**

Este projeto foi concebido sob a ótica de um cientista de dados aplicado ao negócio, equilibrando agilidade, governança e interpretabilidade.

🧠 **Ferramentas e Tecnologias**

**Amazon SageMaker Canvas**
Utilizado como plataforma principal por permitir a criação de modelos de ML de forma low-code, mantendo governança, rastreabilidade e rapidez no ciclo de experimentação.

**AutoML (Canvas)**
O Canvas avaliou múltiplos algoritmos e selecionou automaticamente o modelo com melhor desempenho para os dados tabulares.

**Random Forest**
Algoritmo selecionado pelo Canvas por apresentar:

Robustez a dados ruidosos

Bom desempenho em problemas de regressão

Excelente interpretabilidade via Feature Importance




---

📊 **4. Análise das Variáveis (Feature Importance)**

Feature	Impacto no Modelo	Justificativa de Negócio

vendas_dia	Forte (~45%)	O consumo recente é altamente correlacionado com a demanda futura.
data (sazonalidade)	Médio (~20%)	Captura padrões sazonais e variações ao longo do ano.
promocao	Médio (~15%)	Promoções geram picos significativos de vendas e exigem estoque adicional.


Esses resultados reforçam a coerência entre estatística, Machine Learning e lógica de negócio.


---

🛠️ **5. Estrutura do Repositório**


<img width="915" height="630" alt="Screenshot_20251217-061155" src="https://github.com/user-attachments/assets/c4e20f22-0b21-4dff-a660-41bb2c0b88b3" />



> 📌 Para detalhes sobre cada dataset, consulte: docs/descricao_datasets.md




---

🚀 **6. Como Reproduzir o Projeto**

1️⃣ **Preparação dos Dados**

Execute o notebook:

notebooks/01_data_generation.ipynb

O arquivo principal gerado será:

datasets/raw/estoque_historico_sazonal.csv


2️⃣ **Upload para AWS**

Envie o CSV para um bucket no Amazon S3.


3️⃣ **Treinamento no SageMaker Canvas**

Crie um novo modelo no Canvas

Importe o dataset a partir do S3

Defina a variável estoque_final como Target

Execute o Standard Build para maior precisão


4️⃣ **Previsões Futuras**

Utilize o arquivo:

datasets/predict/estoque_previsao_futura.csv

Execute o Predict e exporte os resultados



---

📈 **7. Resultados & Insights de Negócio**

**Performance do Modelo**

O modelo atingiu um R² ≈ 0,69 no conjunto de teste, o que significa que aproximadamente 70% da variabilidade da demanda foi explicada pelo modelo — um resultado consistente para dados reais de varejo, naturalmente ruidosos.

**Valor para o Negócio**

Antecipação de riscos de ruptura de estoque

Melhoria no planejamento de compras

Redução de capital parado

Apoio à tomada de decisão baseada em dados


> 📄 A análise completa encontra-se em: docs/insights.md




---

🔮 **8. Próximos Passos**

Forecast Multissérie: previsão simultânea de múltiplos SKUs

Lead Time Variável: incorporação do tempo de entrega do fornecedor

Monitoramento de Drift: uso do SageMaker Model Monitor

Automação: integração com sistemas de ERP ou BI



---

👨‍💻 **Autor**

Sérgio Santos
Projeto para demonstração de competências em AWS Cloud, Machine Learning e Análise de Negócio.


---

⭐ Este projeto foi desenvolvido com foco em clareza técnica, impacto de negócio e boas práticas de Data Science aplicada.








---

**Contato:**


[![Portfólio Sérgio Santos](https://img.shields.io/badge/Portfólio-Sérgio_Santos-111827?style=for-the-badge&logo=githubpages&logoColor=00eaff)](https://portfoliosantossergio.vercel.app)

[![LinkedIn Sérgio Santos](https://img.shields.io/badge/LinkedIn-Sérgio_Santos-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/santossergioluiz)

---


