# Тестовое задание

## Лицензия

Данный код распространяется в ознакомительных целях и без каких-либо гарантий, что при его использовании в коммерческих целях он будет сколько-нибудь полезным.

## Назначение

Ответ на тестовое задание ["Написание API чата"](https://gist.github.com/KELiON/c30792164943bd74caa136322f822507).

## Стэк

Ruby, Rails, Postgres, Docker.

Для докер-образа использована рыба https://github.com/anansilva/docker-rails-api-postgres-template (репозиторий с заготовкой без указанной лицензии).

Шаблоны для json используют JBuilder и хранятся в `app/views`.

## Запуск

- Установить Docker, Docker Compose.
- Перейти в папку проекта
- Выполнить
 `docker-compose build`
 `docker-compose up`
 `docker exec appbooster_web_1 rake db:create`
 `docker exec appbooster_web_1 rake db:migrate`
 `docker exec appbooster_web_1 rake db:seed`
- По адресу http://localhost:3000 должно стать доступно API.

## Оценка потенциала масштабирования

- Не представляется возможным шардировать базу данных по сценарию мультитенантного приложения с созданием полностью самодостаточных кластеров - поскольку в исходном ТЗ не заявлена сегментация пользователей по раздельным компаниям. Однако можно разделить некоторые таблицы для улучшения производительности (например, уменьшения размеров).
- Партиционирование таблицы `messages` возможно по полям `created_at`, `chat_id` (таким образом, наиболее частым действием ожидается запрос последних сообщений в выбранном чате).
- Таблицу `message_viewers` можно партиционировать по полям `created_at`, `chat_id`
- Расширение с добавлением групповых чатов: например, признак группового чата можно определить как непустое поле `chats`.`title` (для user2user чатов оставлено пустым).
- Прочитанные сообщения помечаются записью в таблице `message_viewers`. Это избыточно для чатов user2user, но оставляет задел для групповых чатов. (Возможное улучшение: хранить групповые и пользовательские чаты в разных таблицах, таким образом сообщения в чате user2user можно будет хранить метку прочтения прямо у строки БД с сообщением.)
- При добавлении хранения аудио/фото/видео, возможно, придётся добавлять хранение в облаке или хранить/передавать файлы у пользователей по p2p принципу.

## Возможные улучшения

- uuid вместо инкрементальных `id` у сущностей.
- Поддержка Unicode и эмодзи в поле `messages`.`content`
- Написать эндпойнт API для маркировки сообщений прочитанными пользователем и тесты к нему.
- Написать эндпойнт API для отправки сообщения в чат и тесты к нему.
- Написать блок настроек базы данных для продакшна с импортом доступов из переменных окружения согласно правилам двенадцатифакторного приложения.

## Линтер

`docker exec appbooster_web_1 rubocop`

## Тесты

`docker exec appbooster_web_1 bundle exec rspec`

## Документация API

http://localhost:3000/apipie
