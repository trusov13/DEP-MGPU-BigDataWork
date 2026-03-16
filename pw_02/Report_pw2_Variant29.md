# Отчет по Практической работе №2 (Вариант 29)

**Студент:** [ФИО]  
**Группа:** [Номер группы]  
**Вариант:** 29  

---

## Содержимое выполненной работы (на основе Jupyter Notebook)

Ниже приведены материалы выполнения лабораторной работы, извлеченные из предоставленного ноутбука.

---

# Практическая работа 2 — NoSQL
**Вариант 29** — полностью исправленная версия

MongoDB + геопоиск • GraphDB 

---

!pip install Faker pymongo SPARQLWrapper pandas matplotlib seaborn -q

---

from pymongo import MongoClient
from faker import Faker
import random
from SPARQLWrapper import SPARQLWrapper, JSON
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

print("Библиотеки готовы")

---

# ==========================================
# 1. MongoDB (как у преподавателя)
# ==========================================
mongo_client = MongoClient("mongodb://root:abc123!@localhost:27017/")
db = mongo_client["streaming_db"]
users = db["users"]
users.drop()
print("MongoDB подключена (root:abc123!)")

---

# ==========================================
# 2. Генерация пользователей
# ==========================================
fake = Faker()
cities = [("Москва",37.6173,55.7558),("Берлин",13.4050,52.5200),("Париж",2.3522,48.8566),
          ("Рим",12.4964,41.9028),("Мадрид",-3.7038,40.4168),("Лондон",-0.1276,51.5074)]

data = []
for _ in range(20):
    city = random.choice(cities)
    user = {"name": fake.name(), "email": fake.email(), "city": city[0],
            "location": {"type": "Point", "coordinates": [city[1], city[2]]}}
    data.append(user)

users.insert_many(data)
users.create_index([("location", "2dsphere")])
print(f"Создано {len(data)} пользователей + геоиндекс")

---

# ==========================================
# 3. Геопоиск
# ==========================================
results = users.find({"location": {"$near": {"$geometry": {"type": "Point", "coordinates": [37.6173, 55.7558]}, "$maxDistance": 1000000}}})
print("Пользователи рядом с Москвой:")
for u in list(results)[:10]:
    print(f"• {u['name']} | {u['city']}")

---

## Задание 2. GraphDB (порт 17200)

---

# ==========================================
# 4. ГЕНЕРАЦИЯ RDF
# ==========================================
rdf_lines = [
    "@prefix ex: <http://example.org/streaming#> .",
    "@prefix dbo: <http://dbpedia.org/ontology/> .",
    "@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .",
    "@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .",
    "",
    "ex:Drama rdfs:label \"Drama\"@en .",
    ""
]

films = [
    ("Movie1", "The Godfather", 9.2, 8900, "1972-01-01"),
    ("Movie2", "The Shawshank Redemption", 9.3, 12000, "1994-01-01"),
    ("Movie3", "Casablanca", 8.5, 6800, "1942-01-01"),
    ("Movie4", "12 Angry Men", 9.0, 5500, "1957-01-01"),
    ("Movie5", "Schindler's List", 9.0, 7200, "1993-01-01"),
    ("Movie6", "One Flew Over the Cuckoo's Nest", 8.7, 4900, "1975-01-01"),
    ("Movie7", "Citizen Kane", 8.3, 4500, "1941-01-01"),
    ("Movie8", "Psycho", 8.5, 6200, "1960-01-01"),
    ("Movie9", "The Godfather Part II", 9.0, 5300, "1974-01-01"),
    ("Movie10", "Rear Window", 8.5, 4100, "1954-01-01"),
    ("Movie11", "The Room", 3.2, 12500, "2003-01-01"),
    ("Movie12", "Birdemic", 1.9, 6400, "2010-01-01"),
    ("Movie13", "Troll 2", 2.8, 8700, "1990-01-01")
]

for mid, title, rating, comments, date in films:
    rdf_lines.append(f'ex:{mid} dbo:title "{title}" ;')
    rdf_lines.append(f'    dbo:rating "{rating}"^^xsd:float ;')
    rdf_lines.append(f'    dbo:commentCount "{comments}"^^xsd:integer ;')
    rdf_lines.append(f'    dbo:genre ex:Drama ;')
    rdf_lines.append(f'    dbo:releaseDate "{date}"^^xsd:date .')
    rdf_lines.append("")

# Записываем файл чисто
with open("variant29_movies.ttl", "w", encoding="utf-8") as f:
    f.write("\n".join(rdf_lines))

print("✅ Файл variant29_movies.ttl создан успешно")

---

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

---

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

---

# График + вывод
sns.set_theme(style="whitegrid")
plt.figure(figsize=(12,6))
sns.scatterplot(data=df, x="rating", y="comments", s=100, color="red")
plt.title("Феномен «так плохо, что даже хорошо»")
plt.show()

print("\nБИЗНЕС-ВЫВОД: Такие фильмы идеально подходят для раздела 'Культ трэша' — они генерируют огромный UGC и удержание пользователей.")

---

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
