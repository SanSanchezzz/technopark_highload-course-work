# Clubhouse
Курсовая работа по курсу highload в технопарке

## Аудитория
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

