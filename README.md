# MTS тестовое задание
#### Описание решения п. 5
Можно использовать любую доступную очередь, которая бы контролировала поток входящих json-файлов, по идее, даже если файл будет размером х10 - он будет все равно парситься в секунду  

С моей точки зрения удобно было бы хранить в df готовые справочники (в данном примере для других выборок), чтобы каждый раз не заливать txt (или любые другие форматы) файлы в df слой

Jupyter Notebook: <https://github.com/meanother/MTS_testcase/blob/master/testcase.ipynb>
> Из каких компонентов должно состоять решение:
1.  Парсинг json файла, загрузка в df
2.  Преобразование из ненормализованного в виде в нормализованный
3.  Преобразование нормализованных данных в вид, требуемый для обратоки кейса (разбить содержимое поля на отдельные слова для последующего джоина)
4.  Выгрузка требуемых результатов согласно договоренностям с пользователями (выгружать в csv.gz и передавать файл в другую систему, для последующей загрузки в БД, на основе которой строится отчетность
или писать "готовые" куски данных в кафка-топик, который бы вычитывали одна или несколько систем)
5.  В моем понимании на выходе должен быть нормализованный, очищенный от мусора кусок данных, который смог бы прочитать человек или сложить в базу
 
### Описание задания
Необходимо отправить в качестве ответа Jupyter Notebook, в котором при помощи функций фреймворка PySpark будут реализованы:
1.  Считывание файла с твитами “three_minutes_tweets.json” в датафрейм, и преобразование его в витрину следующего вида: <screen_name, tweet_text, created_at, country_code, location, lang, tweet_id, display_url>
Поле location необходимо брать из place.full_name
Поле created_at нужно преобразовать в тип timestamp из оригинального поля timestamp_ms
2.  Считывание файла AFINN-111.txt в датафрейм
3.  Подсчет для каждого твита sentiment (Эмоциональной окраски сообщения) на основе AFINN-111.txt
Каждому слову из твита нужно сопоставить оценку, сентиментом твита является суммарная оценка слов в твите
Если твит не содержит слов из словаря, предполагать sentiment = 0
3.  PySpark-запросы (6 штук), которые выводят по 5 наиболее и наименее счастливых стран, локаций и пользователей, а также среднюю эмоциональную окраску их твитов
4.  PySpark-запрос, который выводит наиболее счастливого пользователя, которого упоминали в своих твитах другие пользователи (упоминание производится с помощью конструкции @<screen_name>)
5.  Описание решения, которое позволит выполнять ежедневно анализ согласно п.3 и 4. Из каких компонентов должно состоять решение, из каких шагов должен состоять ETL процесс от обработки входящих файлов до этапа сохранения конечной информации.
- на входе - непрерывный поток на FTP твитов в файлах (tweet.json), с частотой каждые три минуты. Размеры файлов - в среднем x10 от предоставленного сэмпла.
- на выходе - пользователи должны иметь возможность анализировать счастье по странам, локациям, пользователям, отслеживать изменения, собирать статиcтику и т.д. 
Задание рекомендуется выполнить без использования UDF, RDD api и локального питон-кода, всё необходимое для его выполнения есть в pyspark.sql.functions
Результаты всех PySpark-запросов, в том числе считывания исходных файлов в датафреймы, рекомендуется визуализировать с помощью конструкции df.limit(<число_строк>).toPandas()

AFINN ReadMe:
AFINN is a list of English words rated for valence with an integer
between minus five (negative) and plus five (positive). The words have
been manually labeled by Finn Arup Nielsen in 2009-2011. The file
is tab-separated. There are two versions:
AFINN-111: Newest version with 2477 words and phrases.