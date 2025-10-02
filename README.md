# Dashboard | People Analytics

## 1. Contexto

O foco do projeto é simular o cenário real de atuação de um analista de dados na área de People Analytics, utilizando como base de dados o [Employee/HR Dataset (All in One)](https://www.kaggle.com/datasets/ravindrasinghrana/employeedataset), disponível na Kaggle. 

As informações são fictícias, mas reproduzem parte dos dados encontrados no mundo real por um time de People Analytics, organizadas em quatro arquivos:

- `employee_data.csv` : Informações gerais sobre os **funcionários ativos e desligados:** demografia, hierarquia, remuneração, performance, diversidade e detalhes de desligamento
- `employee_engagement_survey_data.csv` : Resultado da **pesquisa de clima** aplicada aos funcionários, que mede: engajamento, satisfação e equilíbrio de vida
- `recruitment_data.csv` : Informações sobre os candidatos em **recrutamento** e o status do processo seletivo em que estão.
- `training_and_development_data.csv` : Oferece uma visão das ações de **treinamento e desenvolvimento** da empresa, resultado e custo por colaborador.

Com base nesses dados, o projeto terá como entregável um *dashboard em Power BI* que explora três frentes principais: ativos, desligados e produtos de RH.

## 2. Principais Insights

### Ativos

1. Apesar da proporção entre homens (56%) e mulheres (44%) ser relativamente equilibrada no corpo geral da empresa, **os cargos executivos são ocupados exclusivamente por homens.**
2. Em relação à remuneração, há equilíbrio no corpo geral da empresa, com 32% a 36% do time em cada uma das três zonas salariais. No entanto, ao observar a segmentação por cargo, **percebe-se que há uma maior concentração de colaboradores de cargos administrativos (47%) e executivos (60%) na zona salarial mais alta (Zona A).**
3. **41% dos colaboradores com pior performance (PIP) estão na zona salarial mais alta (Zona A).**

### Desligados

1. **A média de tempo de casa dos desligados é inferior a 1 ano (0,91),** sendo que 74% dos desligamentos ocorreram em pessoas com menos de 2 anos de empresa.
2. Desde 2022, o número de desligamentos anuais supera o de admissões, sinalizando uma retração no quadro de funcionários — pessoas deixam a empresa, mas não são repostas.
3. Considerando performance e tempo de casa, **23% das saídas por iniciativa da empresa (Involuntary) ocorreram com pessoas avaliadas como “Exceeds” (acima das expectativas).**

### Produtos de RH

1. Apenas 9,4% dos candidatos em recrutamento possuem pretensão salarial abaixo de R$ 1 milhão.
2. O 3º trimestre (Q3) foi o de menor investimento em treinamento, tanto em 2022 quanto em 2023.
3. **22,3% dos treinamentos realizados falharam, gerando um custo de R$ 16 milhões no período de 2022 e 2023.**

## 3. Desolvimento

### 3.1 Análise Exploratória

Para a análise exploratória dos dados disponíveis, dediquei tempo em investigar como cada coluna disponível nos arquivos se conectava (ou não) com as frentes investigadas nesse projeto. O método para isso, foi importar as bases para o *Google Sheets* e utilizar tabelas dinâmicas para entender a distruibuição e características dos dados. 

Durante essa tarefa, anotei as hipóteses e indicadores que surgiam, como por exemplo:

- Qual é a distribuição dos **desligamentos** por tipo, considerando a performance dos colaboradores?
- Qual a distribuição da zona salarial dos **ativos** da empresa, segmentado por cargo?
- Qual o gasto com **treinamento** por resultado?

Seguindo esse processo, obtive o rascunho dos indicadores e hipóteses que seria importante que o projeto investigasse em sua primeira versão (V1) e avancei para a etapa de ETL, no *Power Bi*.

### 3.2 Tratamento e modelagem

Na etapa de tratamento, **iniciei por garantir a integridade dos dados**: ausência de duplicatas, remoção de espaços extras, exclusão das colunas que não seriam utilizadas e revisão do tipo dos dados. Esses passos foram fundamentais para otimizar performance do modelo, assim como a sua organização.

Além disso, **adicionei novas colunas** para aprofundarmos a análise: 

- Generation (Geração): Calcula a geração com base na data de nascimento
- Tenure (Tempo de Casa): Calcula o tempo de casa em anos. Para os desligados, considera-se o tempo que permaneceu na empresa e para os ativos, os anos decorridos desde a data de admissão até a data de extração do snapshot.
- DateSnapshot: Data de referência do snapshot, visto que ele é uma “fotografia” de um momento especifico do quadro de ativos.

Na sequência, parti para a **modelagem star** dos dados, dividindo-os entre: fato, dimensão e snapshot:

- Fato: `fTreinamento`, `fPesquisaClima` e `fRecrutamento` são os dados dos produtos de RH
- Dimensão: **`dCalendário` para análises temporais robustas e `dFuncionário` com informações como - gênero, geração e cargo.
- Snapshot: `sHeadcount` com o registro de entradas e saídas de funcionários em determinado período.
