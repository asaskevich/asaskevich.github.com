---
layout: post
title: "Backbone.js: маршрутизаторы"
description: "Короткая заметка о маршрутизаторах Backbone.js"
comments: true
category: 
 - Backbone
 - JavaScript
 - Routers
---


В данной маленькой заметке рассмотрю примеры использования маршрутизаторов Backbone. 
Часто в веб-приложении необходимо разделить логику, привязав определенные модели и представления или отдельное состояние приложения к определенным адресам. Для всего этого используется объект `Backbone.Router`

<!-- more -->
### Описание маршрутов

Создавая свой маршрутизатор, нужно расширить `Backbone.Router`, добавив в него описания маршрутов и действий:

``` javascript Sample code
var AppRouter = Backbone.Router.extend({
    routes: {
        "someroute": "routeAction", // Соответствует некоторому адресу #someroute и событию routeAction
    }
});
```

Сами маршруты могут быть динамическими:

``` javascript Sample code
"search"           // Например #search
"search/:q"        // Например #search/query
"search/:q/p:page" // Например #search/find_me_something/p10
"download/*path"   // Например #download/path/to/file.txt
```

### Связывание маршрутов и действий

Используя функцию `on` мы должны связать событие перехода по некоторому адресу с некоторым действием. Так, описав в списке маршрутов:

``` javascript Sample code
"profile/:id": "viewProfile"
```

необходимо описать событие:

``` javascript Sample code
router.on('route:viewProfile', function (id) {
    alert("Going to #/profile/:id, where :id = " + id);
});
```

Это означает, что при переходе по адресу `#/profile/10` будет генерироваться событие `route:viewProfile`, которое будет обработано соответствующей функцией, принимающей в качестве аргументов `id` маршрута.
Во время загрузки страницы, после того, как приложение создаст все необходимые ему маршрутизаторы, нужно вызвать `Backbone.history.start()` или `Backbone.history.start({pushState: true})`, чтобы задать начальное состояние приложения.

### Заключение

Как видим, достаточно просто описать все необходимые действия приложения, пользуясь только маршрутизаторами, не используя дополнительные страницы или скрипты.

#### Ссылки

- [Документация Backbone.js](http://backbonejs.ru/)
- [Документация underscore.js](http://underscorejs.ru/)
