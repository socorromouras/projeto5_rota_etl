# ROTA - ETL
Esse projeto corresponde a última fase do bootcamp "Jornada de Dados" da Laboratória Brasil. Trata-se de construir um sistema de tabelas para consultar informações de forma organizada e relacionada que permite unir dados de diferentes maneiras

# **✔Ficha Técnica**: S**istema ETL**

---

## **📰 Título do Projeto**: **Super Store - Sistema ETL**

---

- **🎯 Objetivo:**
    
    Implementação de um sistema ETL com tabelas de fatos e dimensões para a empresa Super Store.
    

---

- **👩🏽‍🦱 Equipe:**
    
    Individual: **Socorro Moura**
    

---

- **⚙ Ferramentas e Tecnologias**:
    - DrawSQL
    - Google Germini
    - Google Slides

---

- **📑 Processamento:**
    - **Projetando o Fluxo de Atualização do Pipeline ETL (Ordem de Dependência)**
        1. **As tabelas de Dimensão precisam ser carregadas/atualizadas antes da Tabela de Fatos.** Isso porque a Tabela de Fatos depende das chaves primárias (PKs) das Dimensões para preencher suas chaves estrangeiras (FKs);
        2. **O Fluxo Básico:**
            1. **Limpeza/Padronização da Fonte (Tabela Stage):** A primeira coisa a ser feita é preparar seus dados brutos.
            2. **Carregamento das Dimensões:** Carregue os dados únicos para cada uma das suas tabelas de dimensão.
            3. **Carregamento da Tabela de Fatos:** Por último, carregue os dados transacionais, referenciando as chaves primárias já existentes nas dimensões.
        3. **Ordem Detalhada de Atualização:**
            1. **Passo 1:** Fonte de Dados Limpa e Padronizada (Staging)
                1. **Ação:** Crie ou atualize a tabela `rota-super-store.superstore.superstore_standardized`.
                2. **Dependências:** Nenhuma. Esta é a fonte inicial de dados para o seu Data Warehouse, onde você aplica as primeiras limpezas (como padronização de capitalização);
            2. **Passo 2**: Carregamento das Tabelas de Dimensão:
                1. Estas tabelas podem ser carregadas em paralelo (se a ferramenta de pipeline permitir) ou em qualquer ordem, pois não dependem umas das outras. No entanto, é importante que **todas as dimensões estejam prontas** antes da `FactSales`;
                2. **Ação:** Preencha as tabelas de dimensão com os dados únicos da `superstore_standardized`, gerando suas chaves primárias (`_pk` ou `_sk`);
                3. **Dependências:** Todas as dimensões dependem da `superstore_standardized`.
                    1. **`DimCustomer`**
                    2. **`DimProduct`**
                    3. **`DimOrder`**
                    4. **`DimShipment`**
                    5. **`DimLocation`**
                    6. **`DimMarket`** (Se você decidir utilizá-la)
                    7. **`DimDate`**: Esta é um caso especial. A `DimDate` é geralmente populada uma única vez, de forma programática (gerando todas as datas para um período futuro), e depois atualizada apenas para adicionar novas datas conforme o tempo avança. Ela não depende dos dados transacionais da `superstore_standardized` para ser criada, apenas para ser populada com os valores relevantes de data.
            3. **Passo 3:** Carregamento da Tabela de Fatos:
                1. Esta é a última tabela a ser carregada, pois ela depende das chaves primárias de todas as dimensões associadas.
                2. **Ação:** Preencha a tabela `FactSales` com as métricas e, crucialmente, as **chaves estrangeiras (FKs)** das dimensões. Para isso, você fará `JOINs` entre a `superstore_standardized` e cada `Dimensão` para buscar as PKs correspondentes e inseri-las como FKs na `FactSales`.
                3. **Dependências:** Depende da `superstore_standardized` E de **todas** as tabelas de dimensão carregadas.
                    1. **`FactSales`:**
                        1. **Exemplo (Lógica para carregar a `FactSales`):**
                        Para cada linha da `superstore_standardized`, você vai:
                            1. Pegar o `customer_id` dela e procurar o `customer_pk` correspondente na `DimCustomer`.
                            2. Pegar o `product_id` dela e procurar o `product_pk` correspondente na `DimProduct`.
                            3. Pegar o `order_id` dela e procurar o `order_pk` correspondente na `DimOrder`.
                            4. Pegar o `ship_mode` dela e procurar o `shipment_pk` correspondente na `DimShipment`.
                            5. Pegar a `order_date` dela e procurar o `date_pk` correspondente na `DimDate` (para a coluna `order_date_pk`).
                            6. Pegar a `ship_date` dela e procurar o `date_pk` correspondente na `DimDate` (para a coluna `ship_date_pk`).
                            7. Pegar o `country`, `state`, `city`, `region` dela e procurar o `location_pk` correspondente na `DimLocation`.
                            8. Pegar o `market` e `market2` dela e procurar o `market_pk` correspondente na `DimMarket`.
                            9. E então, inserir todas as métricas (`sales`, `quantity`, `profit`, etc.) e essas FKs na `FactSales`.

---

- ✔️ **Resultados e Conclusões:**
    1. Este projeto de fluxo fornece um roteiro claro para a ordem ede execução dos scripts de carregamento no BigQuery para construir o Data Warehouse de forma correta e eficiente.

---

- **🔐Limitações/Próximos Passos**: limitações ou desafios encontrados durante o projeto.
    1. Considerações para o **Futuro**: Incremental vs. Full Load:
        1. **Full Load (Carga Total):** Para começar, você provavelmente fará uma carga total, limpando, padronizando e carregando todas as tabelas do zero.
        2. **Incremental Load (Carga Incremental):** Em um pipeline real, você só carregaria os dados novos ou alterados para economizar tempo e recursos. Isso envolveria identificar quais linhas foram adicionadas/modificadas desde a última carga e processar apenas elas. Para dimensões, isso também incluiria a estratégia de Slowing Changing Dimensions (SCD), caso atributos de uma dimensão mudem ao longo do tempo (ex: nome de um cliente muda).

---

- **🔗Links de interesse:**
    - **Report** - [**LINK**](https://www.canva.com/design/DAGrLlbqUl8/YymYoX7SsHm04IZzzTqvzg/view?utm_content=DAGrLlbqUl8&utm_campaign=designshare&utm_medium=link2&utm_source=uniquelinks&utlId=h229f5a605d)
    - **Apresentação** **Vídeo** - Loom - [**LINK**](https://www.loom.com/share/0b47cd90f19d411bb0a2479640b260f6?sid=724b3c64-2427-43bc-98ee-d2b90dab2270)
    - Pré-processamentos - Colab - [**LINK**](https://colab.research.google.com/drive/197KmDsDNQjx69fK8C18y3pubkdeRrCY-?usp=sharing)
    - Apresentação **Slides** - Google - [**LINK**](https://docs.google.com/presentation/d/1G0QkbNVSawZ18NB3iG1LDH89gP_2Q4fu/edit?usp=sharing&ouid=112139985510828764174&rtpof=true&sd=true)
    - Documentação - [**LINK**](https://docs.google.com/document/d/1sjPFGlOw0Jvfone7gm9QVOWnRIItEke2Je_x6tHOPxg/edit?usp=sharing)
- **RELATÓRIO** - [Link](https://www.canva.com/design/DAGsDiF4GI0/ZNa1fdHdc83-9UpAkV5Qfg/view?utm_content=DAGsDiF4GI0&utm_campaign=designshare&utm_medium=link2&utm_source=uniquelinks&utlId=h40d09a42b0)
- Apresentação - Slides - [Link](https://docs.google.com/presentation/d/1Au0cPppyrUjsxSu5cg05aX0yrAHZMCWV_XhSMFRv5O4/edit?usp=sharing)
- Documentação - [Link](https://docs.google.com/document/d/1iilD5HPSJPigssfxwgxgj9hF1iSnVasBSZ0mJBz878c/edit?usp=sharing)
