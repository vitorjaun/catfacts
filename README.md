
# Tarefas da Startup de Fatos sobre Gatos

---

## 1 - Como a startup foi recém criada, ainda não há uma grande demanda pelos dados, então você precisa desenvolver um script Python simples que extraía os dados de fatos sobre gatos (cat facts) da API e salva em um arquivo CSV local.

```python
import requests
import csv

url = "https://cat-fact.herokuapp.com/facts"
response = requests.get(url)

if response.status_code == 200:
    facts = response.json()
    csv_filename = "cat_facts.csv"
    csv_columns = ["_id", "text", "type", "user", "upvotes"]
    
    with open(csv_filename, mode='w', newline='', encoding='utf-8') as file:
        writer = csv.DictWriter(file, fieldnames=csv_columns)
        writer.writeheader()
        for fact in facts:
            writer.writerow({
                "_id": fact.get("_id", ""),
                "text": fact.get("text", ""),
                "type": fact.get("type", ""),
                "user": fact.get("user", ""),
                "upvotes": fact.get("upvotes", 0)
            })
    
    print(f"Dados salvos com sucesso no arquivo {csv_filename}")
else:
    print(f"Falha ao acessar a API. Status: {response.status_code}")
```

---

## 2 - Com o tempo, o aplicativo ganhou popularidade e o número de fatos sobre gatos cresceu exponencialmente. Agora, a solução local não é mais viável e é necessário transpor a solução para a nuvem. Você precisa projetar uma arquitetura na plataforma Google Cloud que seja capaz de extrair, armazenar e disponibilizar os dados para os times de anaytics. Não é necessário implementar ou codificar, apenas desenhar a arquitetura.

O desenho da arquitetura está no arquivo `arquitetura_cat_facts.png`

---

## 3 - Com o tempo, o time de analytics também sentiu necessidade de realizar suas próprias consultas sobre os dados, como a tecnologia mais conhecida por eles é o BigQuery, você precisa especificar o esquema da tabela de dados de fatos sobre gatos (cat facts), inclua os campos, tipos de campos e quaisquer outras considerações necessárias. A especificação pode ser feita por diagrama ou por código.

| Campo           | Formato    | Descrição                                |
|-----------------|------------|------------------------------------------|
| id              | STRING     | Identificador único do fato sobre gatos. |
| text            | STRING     | Texto contendo o fato sobre gatos.       |
| user            | STRING     | ID do usuário que criou o fato.          |
| created_at      | TIMESTAMP  | Data e hora de criação do fato.          |
| updated_at      | TIMESTAMP  | Data e hora de atualização do fato.      |
| status_verified | BOOLEAN    | Indicador se o fato foi verificado.      |

```sql
CREATE OR REPLACE TABLE `uolcatlovers.cat_facts` (
    id STRING NOT NULL,
    text STRING,
    user STRING,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    status_verified BOOLEAN
)
PARTITION BY DATE(created_at)
```

---

## 4 - Apesar dos dados no BigQuery, o time de analytics não está conseguindo realizar as consultas por conta própria e pediu sua ajuda. Crie uma consulta que extraia os fatos que foram atualizados em agosto de 2020 para um estudo de caso demandado por eles.

```sql
SELECT 
    id,
    text,
    user,
    created_at,
    updated_at,
    status_verified
FROM 
    `uolcatlovers.cat_facts`
WHERE 
    EXTRACT(YEAR FROM updated_at) = 2020
    AND EXTRACT(MONTH FROM updated_at) = 8;
```

---

## 5 - O time de desenvolvimento soube da sua habilidade com consultas SQL e decidiu se aproveitar da fila de demandas para solicitar uma amostra da base de fatos sobre gatos (cat facts) para popular o ambiente de QA deles. O time solicitou uma consulta SQL que extraia, de forma aleatória, 10% dos registros da base contendo as informações de texto, data de criação e data de atualização. Uma consideração importante feita pelo time de desenvolvimento é que eles precisam da consulta SQL para extrair os dados para um arquivo CSV separado por vírgulas.

```sql
SELECT 
    text,
    created_at,
    updated_at
FROM 
    `uolcatlovers.cat_facts`
WHERE 
    RAND() <= 0.10
ORDER BY 
    RAND();
```

O formato CSV pode ser gerado ao exportar a consulta no **BigQuery**.

---
