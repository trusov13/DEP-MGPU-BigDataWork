# Отчёт по лабораторной работе №1
**Вариант 29. Загрузка оборудования**
**Студент:** Трусов Артемий Ильич
**Группа:** БД-251м
**Дата:** 01 марта 2026

## 1. Введение
Цель работы: Освоение основ работы с распределенной файловой системой HDFS и фреймворком Apache Spark.
Вариант 29
Источник данных: https://www.kaggle.com/datasets/arnabbiswas1/microsoft-azure-predictive-maintenance

## 2. Ход работы

### 2.1. Развёртывание среды
![Развёртывание среды](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/%D0%A0%D0%B0%D0%B7%D0%B2%D1%91%D1%80%D1%82%D1%8B%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D1%81%D1%80%D0%B5%D0%B4%D1%8B.png)

### 2.2. Работа с HDFS
![mkdir](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/hdfs_mkdir.png)
![input](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/hdfs_input.png)
![output](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/hdfs_output.png)
![chmod](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/hdfs_chmod.png)

### 2.3. Предобработка данных в Spark
![Spark](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/%D0%9F%D1%80%D0%B5%D0%B4%D0%BE%D0%B1%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%BA%D0%B0%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%B2%20Spark.png)

## 3. Анализ и визуализация

### Общая статистика
![Общая статистика](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/%D0%9E%D0%B1%D1%89%D0%B0%D1%8F%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B0.png)

### Загрузка по часам
![Загрузка по часам](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/%D0%97%D0%B0%D0%B3%D1%80%D1%83%D0%B7%D0%BA%D0%B0%20%D0%BF%D0%BE%20%D1%87%D0%B0%D1%81%D0%B0%D0%BC.png)
![hourly](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/hdfs_output_hourly.png)

### Топ-5 машин
![Top5](https://raw.githubusercontent.com/trusov13/DEP-MGPU-BigDataWork/main/pw_01/images/Top5.png)

## 4. Выводы
(твой улучшенный текст)

## Ссылки

Репозиторий: https://github.com/ваш-ник/lab-01-variant29  
Ноутбук: [lab_01_variant29.ipynb](./lab_01_variant29.ipynb)
