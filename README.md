# Clubhouse
Курсовая работа по курсу highload в технопарке

## Аудитория
Clubhouse доступен только на iphone, что значительно ограничивает целевую аудиторию.

Clubhouse был скачан примерно [13,4 миллиона раз](https://www.bbc.com/news/technology-57058516#:~:text=Clubhouse%20had%20about%2013.4%20million,store%20analysts%20Sensor%20Tower%20said.). Еженедельная количество активных пользователей в Clubhouse составляет примерно [10 миллионов](https://backlinko.com/clubhouse-users#clubhouse-usage-statistics) пользователей.

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

Для передачи аудио высокого качества звука необходимо интернет со скоростью [2.4 МБ в минуту](https://bool.dev/news/detail/skolko-vam-nuzhno-trafika-na-audio-i-video-kontent-dlya-vashego-smartfona), то есть 0.32 МБит в секунду.

Из приведенных выше данных можно сделать вывод, что суммарный суточный траффик составляет примерно 886704 Гбайт в сутки.

### Продуктовые метрики
* Месячная аудиория

Месячная аудиория clubhouse составляет примерно 13 миллионов пользователей.

* Средний размер хранилища пользователя

Допустим, что пользователь clubhouse создаёт хотя бы одну комнату и подписан в среднем на 5 сторонних комнат.

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

Из приведенных выше данных можно посчитать, что средний размер хранилища одного пользователя составляет 1127 байт.

* Среднее количество действий пользователя по типам в день

Каждый день пользователь производит логин и логаут - это два запроса.


Чтобы подключиться к комнате происходит запрос на добавление, запрос об информации комнаты и запрос на отключение. В день пользователь просматривает примерно 6 комнат, в которых, положим, просматривает профиль двух спикеров. Получается, что происходит 18 запросов на подключения к комнатам и ещё 12 запросов на получения информации о спикерах.

В среднем в день от пользователя происходит 32 запроса.

Расчитаем пиковую нагрузку:


`
      32 * 10000000 / 24 / 60 / 60 = 3704 RPS
`

### Технические метрики

* Размер хранения в разбивке по типам данных



## Логическая схема БД
На рисунке ниже представлена логическая схема базы данных.


![image](https://github.com/SanSanchezzz/technopark_highload-course-work/blob/master/png/DBL.png?raw=true)


## Физическая схема БД
На рисунке ниже представлена физическая схема базы данных.

![image](https://github.com/SanSanchezzz/technopark_highload-course-work/blob/master/png/dbp.png?raw=true)

В clubhouse чаще всего должны происходить запросы на авторизацию и подключения к комнатам, по этим причинам логичным решением будет вынести сущности session и user_room в in-memory базу данных redis. Остальные сущности будем хранить в СУБД Postgres, так как это является одним из самых быстрых решений.

## Выбор технологий
