### Previsão de Estoque Inteligente na AWS com Sagemaker Canvas

![NexaIAG](https://github.com/user-attachments/assets/c556e8a2-8292-49c3-aba4-892086424724)


**Bootcamp Nexa - Machine Learning e GenAI na Prática.**

---

# 📦 Previsão de Estoque Inteligente na AWS com SageMaker Canvas

[![AWS Certified](https://img.shields.io/badge/AWS-SageMaker_Canvas-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/sagemaker/canvas/)
[![Machine Learning](https://img.shields.io/badge/Machine_Learning-Regressão-blue?style=for-the-badge)](https://en.wikipedia.org/wiki/Regression_analysis)
[![Status](https://img.shields.io/badge/Status-Pronto_para_Deploy-green?style=for-the-badge)]()

## 🎯 Visão Geral do Projeto

Este projeto demonstra a aplicação de Machine Learning (ML) **No-Code** para resolver um problema de negócio crítico: **Previsão de Estoque e Demanda**. Utilizando o **Amazon SageMaker Canvas**, desenvolvemos um modelo de Regressão capaz de prever a demanda futura (`Previsao_Demanda_30D`) com base em dados históricos com sazonalidade, otimizando a gestão da cadeia de suprimentos.

O foco principal é o treinamento e a documentação do processo de modelagem, além da validação da qualidade dos dados.

## ⚙️ Estrutura do Repositório


<img width="940" height="1012" alt="Screenshot_20251214-062023" src="https://github.com/user-attachments/assets/a1c781d9-d93b-4019-97d5-308277a2c218" />


---


**Documentação principal.**


## 📈 Análise Exploratória de Dados (EDA)

Antes da modelagem, foi realizada uma EDA detalhada nos dados de entrada (`estoque_historico_sazonal.csv`) para confirmar a qualidade e as características do dataset:

* **Sazonalidade Confirmada:** O relatório **`Analise_de_Sazonalidade.pdf`** mostra picos claros de vendas anuais (fim e meio do ano) e semanais (finais de semana), indicando que a coluna `Data` é um preditor crucial.
* **Fatores de Demanda:** A análise no **`Vendas_Historicas_Longo_do_Tempo.pdf`** demonstra que a ativação de promoções é um forte gatilho de aumento de vendas, o que valida a inclusão da coluna `Promocao_Ativa` no modelo.
* **Variação Adequada:** As estatísticas descritivas (em `Analise_de_Estoque_Demanda.pdf`) mostram um desvio padrão de **46.18** nas vendas, indicando uma variação robusta que é ideal para o aprendizado do modelo de Machine Learning.

## 🚀 Guia Passo a Passo (Execução do Projeto e ML)

### 1. Preparação e Configuração do Dataset

* **Geração:** O dataset foi criado e carregado no **Amazon S3**.
* **Importação no Canvas:** O arquivo `estoque_historico_sazonal.csv` foi importado do S3 para a aba **Datasets** do Canvas.

### 2. Construção do Modelo de Regressão

1.  **Criação:** Criado um novo modelo (`Previsao_Demanda_Model`).
2.  **Alvo:** A coluna **`Previsao_Demanda_30D`** foi definida como o alvo de Regressão.
3.  **Features:** Todas as colunas de *input* foram mantidas (`Data`, `Vendas_Historicas_Unidades`, `Preco_Unitario`, etc.), confiando no *feature engineering* automático do Canvas para extrair o valor preditivo da sazonalidade da coluna `Data`.
4.  **Treinamento:** Utilizado o **Standard build** para máxima precisão.

### 3. Análise de Desempenho (Analyze)

* **Métricas de Desempenho:**
    * **RMSE:** **`[VALOR DO RMSE]`**
    * **R-quadrado ($R^2$):** **`[VALOR DO R2]`**
    * *Ação:* Captura de tela das métricas salva em `artifacts/model_performance_metrics.pdf`.

* **Importância de Características:**
    * A análise confirmou que **`[COLUNA MAIS IMPORTANTE]`** (juntamente com as características temporais) foi o fator mais decisivo na previsão.
    * *Ação:* Gráfico de importância salvo em `artifacts/feature_importance.png`.

### 4. Geração de Previsões (Predict)

* O modelo treinado foi utilizado para realizar a **Batch prediction** (previsão em lote) em um novo arquivo de dados futuros.
* O resultado da previsão foi exportado para **`predictions/predictions_results.csv`**.

## 💡 Conclusões de Negócio

O modelo provou ser uma ferramenta eficaz, permitindo que a empresa passe de uma gestão de estoque reativa para uma **preditiva**. A análise demonstrou que a sazonalidade e a estratégia de preços são fatores-chave para otimizar o estoque.

---








---

**Contato:**


[![Portfólio Sérgio Santos](https://img.shields.io/badge/Portfólio-Sérgio_Santos-111827?style=for-the-badge&logo=githubpages&logoColor=00eaff)](https://santosdevbjj.github.io/portfolio/)
[![LinkedIn Sérgio Santos](https://img.shields.io/badge/LinkedIn-Sérgio_Santos-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/santossergioluiz) 

---


