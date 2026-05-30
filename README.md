# 📊 Dashboard de Produtividade — Reestoque & Found | Mercado Livre

> Solução completa de Business Intelligence para monitoramento de produtividade operacional em tempo real, desenvolvida com BigQuery, Google Sheets e Looker Studio.

---

## 🎯 O Problema

O time de inventário do hub **BRSP06 (Mercado Livre)** realizava movimentações diárias de reestoque e found sem visibilidade centralizada dos dados. Não havia como responder perguntas como:

- Quem está batendo meta? Quem está abaixo?
- Qual família de produto concentra mais movimentações?
- Qual o horário de pico de produtividade do time?
- Qual o impacto individual de cada colaborador no total?

---

## 💡 A Solução

Desenvolvi um pipeline completo de dados — do banco de dados ao dashboard visual — que permite acompanhar a produtividade do time **em tempo real**.

---

## 🔧 Tecnologias Utilizadas

| Ferramenta | Uso |
|---|---|
| **BigQuery** | Banco de dados e processamento da query SQL |
| **Google Sheets** | Camada de extração e enriquecimento dos dados |
| **Looker Studio** | Visualização e dashboard interativo |

---

## ⚙️ Como Funciona — Arquitetura do Pipeline

```
BigQuery (banco de dados)
    ↓ Query SQL customizada
Google Sheets (folha vinculada em tempo real)
    ↓ PROCV para enriquecer com líder + família do produto
Looker Studio (dashboard interativo)
```

### 1️⃣ Query SQL no BigQuery

```sql
SELECT
    DATETIME_ADD(FBM_CREATED_DATE, INTERVAL 30 MINUTE) AS DATA,
    EXTRACT(HOUR FROM DATETIME_ADD(FBM_CREATED_DATE, INTERVAL 30 MINUTE)) AS HORA,
    FBM_USER_ID AS ID_GROOT,
    FBM_REASON_PROCESS AS PROCESSO,
    INVENTORY_ID,
    ADDRESS_FROM,
    ADDRESS_TO,
    SUM(FBM_QUANTITY) AS PECAS

FROM meli-bi-data.WHOWNER.BT_FBM_STOCK_3_MOVEMENT

WHERE WAREHOUSE_ID = 'BRSP06'                        -- Hub específico
    AND DATE(FBM_CREATED_DATE) >= CURRENT_DATE - 7   -- Últimos 7 dias
    AND FBM_USER_ID IN (...)                          -- Time filtrado
    AND FBM_REASON_PROCESS IN ('found', 'transfer')  -- Processos relevantes

GROUP BY 1,2,3,4,5,6,7
```

### 2️⃣ Google Sheets — Extração e Enriquecimento

- Folha vinculada diretamente ao BigQuery com **atualização automática**
- Página de extração criada para manipulação dos dados
- PROCV para adicionar automaticamente o **líder de cada colaborador** e a **família do produto**

### 3️⃣ Looker Studio — Dashboard Final

Dashboard interativo com filtros dinâmicos por: período, processo, colaborador, líder, família de produto e hora.

---

## 📈 Resultados no Dashboard

| Indicador | Valor |
|---|---|
| Total de peças armazenadas | 26.259 |
| % Atingimento da meta | 112,22% |
| Colaboradores ativos | 39 |
| Média por colaborador | 673,31 peças |

### Visões disponíveis:
- 📅 Performance diária vs meta
- 🥧 Distribuição por família de produto
- 👤 Performance individual com status e medidor

---

## 🧠 Aprendizados e Diferenciais

- Integração **BigQuery → Sheets → Looker** sem nenhuma exportação manual
- Dados atualizados em tempo real via folha vinculada
- Análise horária com ajuste de fuso (+30 min) direto na query
- Dashboard utilizado ativamente pelo time de gestão do hub

> 📎 *Prints do dashboard, da query e da planilha em anexo.*

---

## 👨‍💻 Autor

**Hiago Prates** — Analista Operacional | Estudante de Ciência de Dados
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/hiago-prates-a3a295400)
