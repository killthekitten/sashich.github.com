---
layout: post
title: "Структура модульного приложения на Sinatra"
description: ""
category: code
tags: [sinatra, ruby]
post_author: Александр Чайчук
post_gravatar: 73607c45ffe82abc614c8e28012cf8bd
---
{% include JB/setup %}

Приложения на Sinatra можно писать используя два стиля: модульный и классический. Разница между ними небольшая, подробнее [здесь](http://goo.gl/GoKT7). 

Не сильно вдаваясь в мотивы использования модульного стиля, я опишу только структуру самого приложения. 

### Задача
И так, нам необходимо иметь несколько изолированных микро-приложений, работающих под высокой нагрузкой. Кода немного, райнтайм идентичный, логично держать эти приложения в одном репозитории в виде модульного Sinatra приложения. Каждый модуль инкапсулирует нехитрую бизнес-логику и представляет простейший HTTP обработчик.

Наше приложение это простой [ad-serving](http://en.wikipedia.org/wiki/Ad_serving) движок и [tracking пиксель](http://en.wikipedia.org/wiki/Web_bug). Приложение умеет отдавать креативы, считать показы и клики.

### Структура приложения 

Для деплоя мы используем capistrano, хоть это и избыточно (можно обойтис munin'ом), в качестве rack сервера пока unicorn.

    |
    |-- config
    |   |-- deploy.rb
    |   |-- unicorn.rb
    |--lib
    |  |-- concerns
    |  |  |-- logger.rb
    |  |  |-- init.rb     
    |  |-- models
    |  |  |-- site.rb
    |  |  |-- banner.rb
    |  |  |-- init.rb
    |  |-- routes 
    |  |  |-- clicks.rb
    |  |  |-- imressions.rb
    |  |  |-- banners.rb
    |  |  |-- init.rb
    |--specs
    |-- app.rb
    |-- config.ru
    |-- Capfile

Вроде все просто. А что внутри?
<script src="https://gist.github.com/3772032.js?file=app.rb">
</script>


Примерно так выглядят наши обработчики:
<script src="https://gist.github.com/3772032.js?file=routes.rb">
</script>

Чтобы не загромождать app.rb кучей вызовов require, в каждой папке лежит init.rb, который загружает .rb 
файлы по отдельности.
<script src="https://gist.github.com/3772032.js?file=init.rb">
</script>

И еще rackup файл:
<script src="https://gist.github.com/3772032.js?file=config.ru">
</script>

Наш подход немного оличается от предложенного в документации: мы не подключаем обработчики как middleware, но сути это не меняет.

Возможно, в будущем мы откажемся от Sinatra в пользу evented сервера, но благодаря использованию модульного стиля switch cost будет не высоким.














