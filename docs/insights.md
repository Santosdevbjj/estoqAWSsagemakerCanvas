 **Análise de Negócio — Previsão de Estoque Inteligente**

 **Contexto**

Este documento apresenta a análise de negócio baseada nos resultados obtidos com o modelo de Machine Learning treinado no Amazon SageMaker Canvas, utilizando o dataset estoque_historico_sazonal.csv.

O objetivo desta análise é transformar as previsões geradas pelo modelo em insights acionáveis, apoiando decisões estratégicas de reposições de estoque, planejamento de demanda e redução de custos operacionais.


---

 **Objetivo do Modelo**

O modelo foi treinado para prever a variável:

estoque_final

Essa previsão permite responder perguntas críticas do negócio, como:

Quando será necessário repor estoque?

Quais produtos têm maior risco de ruptura?

Como a sazonalidade impacta o consumo?

Qual o impacto das promoções sobre o estoque?



---

 **Principais Variáveis que Influenciam o Estoque**

Com base na análise de Feature Importance fornecida pelo SageMaker Canvas, destacam-se:

🔹 **1. vendas_dia**

Variável com maior impacto na previsão do estoque final

Altas vendas reduzem rapidamente o estoque disponível

Fundamental para prever rupturas


🔹 **2. estoque_inicial**

Estoques iniciais mais altos reduzem o risco de ruptura

Porém, excesso de estoque pode gerar custos de armazenagem


🔹 **3. promocao**

Promoções aumentam significativamente as vendas

Produtos em promoção exigem planejamento antecipado de reposição


🔹 **4. data (sazonalidade)**

O modelo identificou padrões sazonais claros:

Fim de ano

Datas comemorativas

Períodos de maior consumo




---

 **Análise de Sazonalidade**

O dataset foi construído com sazonalidade anual real, permitindo ao modelo aprender padrões como:

Aumento de vendas no final do ano

Picos de consumo em determinados períodos

Redução de vendas em meses específicos


 **Insight-chave**

> Produtos apresentam comportamento previsível ao longo do ano, permitindo ações proativas de reposição.




---

 **Análise por Categoria de Produto**

 **Eletrônicos**

Alta sensibilidade a promoções

Estoque precisa ser ajustado em períodos promocionais

Risco elevado de ruptura sem planejamento


 **Alimentos**

Alta rotatividade

Demanda mais constante

Reposição frequente e automática é recomendada


 **Vestuário**

Forte influência sazonal

Estoque deve ser ajustado conforme estações do ano



---

 **Risco de Ruptura de Estoque**

O modelo permite identificar cenários críticos:

Estoque final próximo de zero

Alta demanda combinada com promoção

Períodos sazonais de pico


 **Insight-chave**

> Antecipar reposições em períodos sazonais reduz perdas de venda e insatisfação do cliente.




---

 **Impacto Financeiro**

Com base nas previsões:

Redução de perdas por ruptura de estoque

Otimização de capital parado

Melhoria no planejamento logístico

Aumento da previsibilidade operacional



---

 **Confiabilidade do Modelo**

As métricas avaliadas no SageMaker Canvas indicam:

Boa capacidade de generalização

Baixo erro médio (MAE/RMSE)

Alta aderência aos padrões históricos


 O modelo é adequado para apoio à decisão, não substituindo análises humanas, mas potencializando-as.


---

 **Recomendações Estratégicas**

1. Integrar o modelo ao planejamento de compras


2. Usar previsões para definir pontos de reposição


3. Ajustar estoques antes de campanhas promocionais


4. Monitorar sazonalidade continuamente


5. Re-treinar o modelo periodicamente




---

 **Conclusão Executiva**

O uso do Amazon SageMaker Canvas demonstrou ser uma solução eficaz para:

Criar modelos de ML sem código

Gerar previsões confiáveis de estoque

Apoiar decisões estratégicas de negócio


>  A Previsão de Estoque Inteligente permite sair do reativo para o preditivo, trazendo vantagem competitiva, redução de custos e maior eficiência operacional.




---

• **Autor**

Sérgio Santos
Especialista em Tecnologia, Dados e Cloud Computing


---


