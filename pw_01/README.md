# Практическая работа 1. Обработка и анализ данных с использованием Apache Spark (PySpark).
# Вариант 29 
# Выполнил студент Трусов Артемий Ильич   
**Загрузка оборудования**

**Цель работы:** Освоение основ работы с распределенной файловой системой HDFS и фреймворком Apache Spark.

## Описание задачи

**Вариант 29** — Загрузка оборудования  
**Бизнес-задачи:**
1. Определить время простоя оборудования
2. Рассчитать загрузку по часам
3. Построить график загрузки

**Источник данных**  
Microsoft Azure Predictive Maintenance Dataset  
Ссылка: https://www.kaggle.com/datasets/arnabbiswas1/microsoft-azure-predictive-maintenance  
Основные файлы:  
- PdM_telemetry.csv (~76 МБ) — почасовая телеметрия  
- PdM_failures.csv (~24 КБ) — события отказов

## 📌 Структура проекта

- 📄 [README.md](./README.md)
- 📄 [Report.md](./Report.md)
- 📓 [lab_01_variant29.ipynb](./lab_01_variant29.ipynb)
- [/images](./images/)

    
## Как запустить проект

### Требования
- ОС: Ubuntu 20.04+ (или образ ds_mgpu_Hadoop3+spark_3_4)
- Hadoop 3.x
- Spark 3.4+
- Python 3.10–3.12
- Jupyter Notebook / JupyterLab
- Установленные библиотеки: pyspark, pandas, matplotlib, seaborn, numpy

### Шаги запуска

1. Запустить Hadoop и YARN (от пользователя hadoop)
   ```bash
   sudo su - hadoop
   start-dfs.sh
   start-yarn.sh
   jps                # проверка процессов
   ```

2. Создать директории и загрузить данные в HDFS
	```bash
	hdfs dfs -mkdir -p /user/hadoop/lab_01_variant29/input
	hdfs dfs -put PdM_telemetry.csv PdM_failures.csv /user/hadoop/lab_01_variant29/input/
	hdfs dfs -ls /user/hadoop/lab_01_variant29/input/
	```
3. Запустить Jupyter
4. Остановить кластер после работы
	```bash
	stop-yarn.sh
	stop-dfs.sh

	```

