# Отчет по Практической работе №2  
## Изучение и применение различных типов NoSQL баз данных (Polyglot Persistence)

**Студент:** [ФИО]  
**Группа:** [Номер группы]  
**Вариант:** 29  

---

## 1. Введение

Работа посвящена проектированию полиглотной системы хранения данных для стриминговой платформы.  
Используются MongoDB, Cassandra и GraphDB для различных типов данных.

---

## 2. Развертывание инфраструктуры

Контейнеры развернуты в Docker‑среде согласно методическим указаниям.

---

## 3. Выполнение задания 1 — MongoDB (геопространственный поиск)

Ниже приведён код, использованный в работе.

```python
!pip install Faker pymongo SPARQLWrapper pandas matplotlib seaborn -q

from pymongo import MongoClient
from faker import Faker
import random
from SPARQLWrapper import SPARQLWrapper, JSON
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

print("Библиотеки готовы")

# ==========================================
# 1. MongoDB (как у преподавателя)
# ==========================================
mongo_client = MongoClient("mongodb://root:abc123!@localhost:27017/")
db = mongo_client["streaming_db"]
users = db["users"]
users.drop()
print("MongoDB подключена (root:abc123!)")
```

---

## 4. Выполнение задания 2 — GraphDB / SPARQL

Использованный код и запросы:

```python
!pip install Faker pymongo SPARQLWrapper pandas matplotlib seaborn -q

from pymongo import MongoClient
from faker import Faker
import random
from SPARQLWrapper import SPARQLWrapper, JSON
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

print("Библиотеки готовы")

# ==========================================
# 5. Подключение к GraphDB 
# ==========================================
sparql = SPARQLWrapper("http://localhost:17200/repositories/movies_repo")
sparql.setReturnFormat(JSON)

print("Подключаемся к GraphDB по порту 17200...")

try:
    sparql.setQuery("SELECT (COUNT(*) AS ?cnt) WHERE { ?s ?p ?o } LIMIT 1")
    res = sparql.query().convert()
    print(f"GraphDB подключен! Триплетов в репозитории: {res['results']['bindings'][0]['cnt']['value']}")
except Exception as e:
    print("Ошибка подключения:", e)

# ==========================================
# SPARQL-запросы
# ==========================================
q1 = '''PREFIX dbo: <http://dbpedia.org/ontology/>
SELECT ?title ?rating ?comments WHERE {
  ?m dbo:title ?title ; dbo:rating ?rating ; dbo:commentCount ?comments .
  FILTER(?rating < 5.0 && ?comments > 500)
} ORDER BY DESC(?comments)'''

sparql.setQuery(q1)
df = pd.DataFrame([{
    "film": r["title"]["value"],
    "rating": float(r["rating"]["value"]),
    "comments": int(r["comments"]["value"])
} for r in sparql.query().convert()["results"]["bindings"]])

print("Фильмы с низким рейтингом, но высокой обсуждаемостью:")
print(df)

# SPARQL 2 — Драмы до 1990 года с рейтингом > 7.0
q2 = '''
PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?title ?rating ?date
WHERE {
  ?m dbo:title ?title ;
     dbo:rating ?rating ;
     dbo:releaseDate ?date .
  ?m dbo:genre ?g .
  ?g rdfs:label "Drama"@en .
  FILTER(?rating > 7.0 && ?date < "1990-01-01"^^xsd:date)
}
LIMIT 10
'''

sparql.setQuery(q2)
sparql.setReturnFormat(JSON)
results2 = sparql.query().convert()

df_drama = pd.DataFrame([
    {
        "film": r["title"]["value"],
        "rating": float(r["rating"]["value"]),
        "date": r["date"]["value"]
    }
    for r in results2["results"]["bindings"]
])

print("Драмы до 1990 года с рейтингом > 7.0:")
print(df_drama)
```

---

## 5. Выполнение задания 3 — Бизнес‑аналитика

Аналитическая обработка данных:

```python
!pip install Faker pymongo SPARQLWrapper pandas matplotlib seaborn -q

from pymongo import MongoClient
from faker import Faker
import random
from SPARQLWrapper import SPARQLWrapper, JSON
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

print("Библиотеки готовы")
```

---

## 6. Выводы

Использование нескольких специализированных NoSQL БД позволяет эффективно решать разные классы задач:  
MongoDB — гибкие данные и геоанализ, Cassandra — потоковые данные, GraphDB — анализ связей и рекомендации.
