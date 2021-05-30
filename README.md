# Clubhouse
Курсовая работа по курсу highload в технопарке

## Аудитория
Clubhouse доступен только на iphone, что значительно ограничивает целевую аудиторию.

Clubhouse был скачан примерно [13,4 миллиона раз](https://www.bbc.com/news/technology-57058516#:~:text=Clubhouse%20had%20about%2013.4%20million,store%20analysts%20Sensor%20Tower%20said.). Еженедельное количество активных пользователей в Clubhouse составляет примерно [10 миллионов](https://backlinko.com/clubhouse-users#clubhouse-usage-statistics) пользователей.

Сегодня clubhouse доступен в [154 странах](https://backlinko.com/clubhouse-users#how-popular). Процентное соотношение пользователей по странам выглядит следующим образом:

United States | Brazil | United Kingdom | India | Canada |
--- | --- | --- | --- | --- |
33.34% | 7.03% | 5.21% | 4.62% | 3.30% |

## MVP
Минимальный функционал clubhouse:
1. Регистрация / авторизация;
2. Создание комнат;
3. Проведение онлайн конференций.

## Расчёт нагрузки
В clubhouse проводятся онлайн лекции и конференции, из чего можно сделать предположение о том, что в большинстве случаев в комнате в каждый момент времени присутствует один спикер, а остальная аудитория его слушает.

В среднем пользователь проводит в комнате [6 минут и 46 секунд](https://www.similarweb.com/website/app.clubhouse.io/) и посещает [5.46 комнат](https://www.similarweb.com/website/app.clubhouse.io/) в день. Следовательно, пользователь проводит в конференциях в clubhouse 36 минут и 57 секунд. Из этих данных получаем, что в неделю один пользователь тратит 258 минут и 38 секунд в clubhouse.


### Продуктовые метрики
* Месячная аудиория

Месячная аудиория clubhouse составляет примерно 13 миллионов пользователей.

* Средний размер хранилища пользователя

Допустим, что пользователь clubhouse создаёт хотя бы одну комнату и подписан в среднем на 7 сторонних комнат.

В таблице ниже приведены данные о размерах полях в базе данных, которая привидена в разделе логической схемы БД.

user_
| Название | Тип         | Размер  |
| -------- | ----------- | ------- |
| id       | int         | 8 байт  |
| fullname | varchar(64) | 64 байт |
| bio      | varchar(64) | 64 байт |
| email    | varchar(64) | 64 байт |
| password | varchar(64) | 64 байт |

session
| Название | Тип  | Размер  |
| -------- | ---- | ------- |
| id | int | 8 байт |
| user_id   | int | 8 байт  |
| expires | timestamp | 8 байт |

room
| Название  | Тип        | Размер          |
| --------- | ---------- | --------------- |
| id        | int       | 8 байт         |
| topic | varchar(64) | 64 байт |
| host | varchar(64) | 64 байт |
| is_public | bool | 1 байт |

user_room
| Название  | Тип        | Размер          |
| --------- | ---------- | --------------- |
| user_id   | int        | 8 байт          |
| room_id   | int        | 8 байт          |
| is_moderator| bool     | 1 байт          |

Из приведенных выше данных можно посчитать, что средний размер хранилища одного пользователя составляет 1161 байт.

* Среднее количество действий пользователя по типам в день

Каждый день пользователь производит логин и логаут - это два запроса.


Чтобы подключиться к комнате происходит запрос на добавление, запрос об информации комнаты и запрос на отключение. В день пользователь просматривает примерно 6 комнат, в которых, положим, просматривает профиль двух спикеров. Получается, что происходит 18 запросов на подключения к комнатам и ещё 12 запросов на получения информации о спикерах.

В среднем в день от пользователя происходит 32 запроса.

Расчитаем пиковую нагрузку:


`
      13.4 * 10^6 / 100 * 20 * 32 / 24 / 60 / 60 = 993 RPS
`

### Технические метрики
Всего в clubhouse зарегистрированно [13,4 миллиона](https://www.bbc.com/news/technology-57058516#:~:text=Clubhouse%20had%20about%2013.4%20million,store%20analysts%20Sensor%20Tower%20said.) пользователей.

Clubhouse доступен только на телефоны от apple, что означает, что у каждого пользователя может быть одновременно только одна сессия.

* Размер хранения в разбивке по типам данных

Посчитаем размер хранилища информации о пользователях:


`
      13,4 * 10^6 (пользователей) * 264 (байт) = 3537600000 Байт = 3,5376 Гигабайт
`

Далее посчитаем размер хранилища пользовательских сессий, учитывая, что при пиковой нагрузке сервисом пользуется 10 миллионов человек:


`
      10 * 10^6 (пользователей) * 24 (байт) = 240000000 Байт = 0,24 Гигабайта
`

Допустим, что каждый активный пользователь создал хотя бы одну комнату. Тогда имеем:

`
      10 *10^6 (пользователей) * 137 (байт) = 1370000000 Байт = 1,37 Гигабайт
`

Считаем, что в среднем каждый пользовател подписан на 7 комнат, получим:

`
      13,4 * 10^6 (пользователей) * 17 (байт) * 7 = 1594600000 Байт = 1,5946 Гигабайт
`

* Сетевой трафик

Для передачи аудио высокого качества звука необходим интернет со скоростью [2.4 МБ в минуту](https://bool.dev/news/detail/skolko-vam-nuzhno-trafika-na-audio-i-video-kontent-dlya-vashego-smartfona), то есть 0.32 МБит в секунду.

Посчитаем суммарный суточный траффик, предполагая что максимальная нагрузка на сервис в сутки - 10000000 миллионов пользователей:

`
      10 * 10^6 * 2.4 * 37 = 888000 Гигабайт в сутки
`

Попробуем предположить пиковое нагрузку в течении суток, для этого воспользуемся законом Парето. Предположим, что 20 процетов от суточного количества активных пользователей clubhouse одновременно будут использовать приложение. Чтобы проверить данно предположение проанализируем статистику от другого высоконагруженного сервиса Discord, в котором количество активных пользователей в сутки составляет [14 миллионов пользователей](https://github.com/NikitaLobaev/Technopark_Highload), а при пиковой нагрузке количество пользователей составляет примерно [2,78 миллиона](https://github.com/NikitaLobaev/Technopark_Highload). Считаем: `14 * 10^6 / 100 * 20 = 2 800 000` пользователей, что примерно составляет 20 процентов от ежедневного количества активных пользователей.

Получаем для clubhouse пиковую нагрузку в 2.68 миллиона пользователей.

Считаем:

`
      2.68 * 10^6 * 2.4 (Мб в минуту) / 60 (Секунд) = 858 Гигабит / С
`

<!-- Из приведенных выше данных можно сделать вывод, что суммарный суточный траффик составляет примерно 886704 Гбайт в сутки. -->

* RPS в разбивке по типам запросов

Запросы будут приходить на основной сервер и на сервера, отвечающие за трансляции.

Приведенные выше данные посчитаем RPS в разбивке по типам запросов:

Для подключения к конференции пользователь должен совершить три запроса (см. выше), ежедневно пользователь заходит в 6 комнат значит:

`
      13.4 * 10^6 / 100 * 20 * 3 * 6 / 24 / 60 / 60 = 589 RPS
`


## Логическая схема БД
На рисунке ниже представлена логическая схема базы данных.


![image](https://github.com/SanSanchezzz/technopark_highload-course-work/blob/master/png/DBL.png?raw=true)


## Физическая схема БД

Учитывая, что объём размера хранилища данных при пиковой нагрузке, который был расчитан выше, не привышает и 10 гигабайтов, будем хранить всё в одной субд. В качестве субд будем использовать Postgres, так как это будет одним из самых быстрых решений. Так как размер базы данных не большой, шардирование не является необходимостью. Создание комнаты происходит на ближайшем к создателю сервере, если он не перегружен.
На рисунке ниже представлена физическая схема базы данных.

![image](https://github.com/SanSanchezzz/technopark_highload-course-work/blob/master/png/dbp.png?raw=true)

Для обеспечения отказоустойчивости будем иметь две реплики, которые будут выступать в роли Slave для основной базы данных.

## Выбор технологий

На самом низком уровне для трансляций в режиме реального времени применятеся протокол WebRTC, который мы и будем использовать. В качестве языка программирования для серверов хорошим вариантом будет golang, так как это язык предназначен для реализвции веб серверов. Клиентское приложение будет реализовано в виде IOS приложений, от чего мы избавляемся от необходимости иметь фронтенд сервера.

## Физическая схема проекта

Рассчитаем конфигурации серверов.

<!-- У сервера хоста звонков есть два узких горлышка: это пропускная способность и количество ядер, для обслуживания достаточного количества пользователей. Используя приведенные выше данные о пиковой нагрузке получим: `10 * 10^6 / 100 * 20 = 2 000 000` пользователей.  -->

<!-- Как было сказано ранее, в каждой в комнате одновременно говорит только один спикер, из этого предположения и будем расчитывать конфигурации серверов. Известно, что из всех пользователей clubhouse спикерами являются только [10 процентов](https://thebell.io/portret-russkoyazychnogo-clubhouse-900-tysyach-polzovatelej-i-risk-ugodit-v-dolinu-smerti). Количество слушателей составляет `2 000 000 / 100 * 90 = 1 800 000` пользователей. Для каждого слушателя необходим только один поток для трансляции ему аудио. Количество спикеров составляет ` 2 000 000 / 100 * 10 = 200 000` пользователей. Для каждого спикера необходимо как минимум два потока: один для трансляции аудио от него, и один для трансляции аудио ему. -->

<!-- Посчитаем необходимое количество потоков для функционирования под пиковой нагрузкой: `1 800 000 * 1 + 200 000 * 2 = 2 200 000` потоков. Одно ядро процессора может обслуживать два потока, исходя из этого нам понадобиться `2 200 000 / 2 = 1 100 000` ядер. Будем брать процессоры на 96 ядер каждый, получается, что нам потребуется `1 100 000 / 96 / 2 = 5 730` процессоров для поддержания хостов комнат. -->

Считаем, что одна сетевая карточка способна выдерживать трафик в 10 гигабит в секунду.

`
      858 / 10 = 86 сетевых карточек.
`

Для обеспечения отказоусточивости будем иметь 20% запас, то есть нам потребуется 104 сетевые карты. 

В один сервер можно уместить две сетевые карты, из чего получем, что нам потребуется 52 сервера, которые стоит распределить равномерно распределить между США и Европой.

Из приведённых выше данных очевидно, что узким местом является количество ядер.

| Роль                                     | CPU     | ОЗУ       | Жёсткий диск | Количество |
| ---------------------------------------- | ------- | --------- | ------------ | ---------- |
| Управляющий сервис                       | 32 (×2) | 32 Гбайт  | 512 Гбайт    | 2 в США + 2 в Европе        |
| СУБД                                     | 32 (×2) | 16 Гбайт  | 128 Гбайт    | 3 (с учётом реплик)        |
| Хост звонков                             | 96 (×2) | 128 Гбайт | 128 Гбайт    | 52     |

Сервера для хостов звонков следует равномерно распределить в Европе и США. Для обеспечения отказоустойчивости будем иметь два управляющих сервера в Нью-Йорке и два во Франкфурте.

![image](https://github.com/SanSanchezzz/technopark_highload-course-work/blob/master/png/project.png?raw=true)

### Алгоритм балансировки звонков на голосовые узлы

Изначально пользователь отправляет запрос DNS сервер, в котором с помощью geo based роутинга выбирается ближайший сервис управляющих звонков, который может находиться в Нью-Йорке или Франкфурте.

После этого пользователь может либо присоединиться к комнате, либо создать комнату / получить информацию о комнатах.

* Присоединение к комнате.

После geo based балансировки запрос пользователя идёт на управляющий сервер, который проверит количество активных пользователей, которое обслуживает хост, и в случае, если на узел приходится большая нагрузка (заканчивается пропускная способность), то пользователю будет отказано в подключении, в противно случае в базу данных добавится запись о новом пользователе и клиенту будет выслан адрес хоста, на котором находится комната и к которому будет происходить подключение.

* Создание комнаты

После geo based балансировки запрос пользователя идёт на управляющий сервер, который определяет ближайший к пользователю хост звонков, проверяет количество комнат на хосте и, в зависимости от нагруженности узла, либо создаёт комнату, либо находит следующий ближайший хост.

Для всех остальных операций пользователь после geo based балансировки обращается к управляющему серверу, который и обрабатывает запрос.
