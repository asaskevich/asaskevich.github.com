---
layout: post
title: "localForage – храним данные offline без головной боли. Удобная абстракция над IndexedDB"
date: 2015-04-21 22:48:38 +0300
comments: true
categories: 
  localStorage
  localForage
  indexedDB
  webSql
---

Большинство современных веб-приложений каким-либо образом хранят отдельные данные у клиента, пользуясь такими средствами как localStorage, WebSQL или IndexedDB. Так, например, для небольших HTML5-игр весьма удобно хранить результаты игрока локально - не нужно писать взаимодействие с базами данных, а сохранив все локально, игрок легко может продолжить прогресс с места своего сохранения. 
Однако несмотря на удобство, возникает проблема - хранилища реализуют схожий функционал, однако методы,предлагаемые API, весьма различаются. Это создает проблемы конечному разработчику, который вынужден держать в голове список всех функций,их аргументов и т.д. Помимо этого каждое хранилище имеет свои достоинства и недостатки, что также сказывается на разработке. По этой причине Mozilla представила свою библиотеку [localForage](http://mozilla.github.io/localForage/), позволяющую забыть о таких мелочах и сосредоточиться на реализации функционала. Рассмотрим ее возможности подробнее.
<!-- more -->

#### Установка
localForage может быть установлен двумя способами - при помощи bower либо напрямую, скачав непосредственно минифицированную версию скрипта:
``` cpp Sample code
bower install localforage
```

#### Работа с данными
Вообще говоря, localForage позволяет хранить данные любого типа, будь то строки, числа, массивы, либо бинарные объекты. Список хранимых типов представлен ниже:

* Array
* ArrayBuffer
* Blob
* Float32Array
* Float64Array
* Int8Array
* Int16Array
* Int32Array
* Number
* Object
* Uint8Array
* Uint8ClampedArray
* Uint16Array
* Uint32Array
* String

Для добавления и получения информации по некоторому ключу мы можем воспользоваться интуитивными методами `setItem` и `getItem`:
``` js Sample code
localforage.setItem('key', 'some value', function(err, value) {
    console.log(value);
});
localforage.getItem('key', function(err, value) {
    console.log(value);
});
```
Также легко мы можем удалить элементы по ключу:
``` js Sample code
localforage.removeItem('key', function(err) {
    console.log('Key is cleared!');
});
```
Помимо того мы можем очистить все хранилище, получить его размер, ключи, а также провести итерацию по всем ключам/значениям.

#### Настройка localForage
Простейшим образом мы можем указать список предпочитаемых хранилищ, используемых библиотекой:
``` js Sample code
localforage.setDriver(localforage.LOCALSTORAGE);
// А таким образом мы указываем список предпочитаемых хранилищ
localforage.setDriver([localforage.WEBSQL, localforage.INDEXEDDB]);
```
Также мы можем указать название хранилища, его размер, версию и описание, воспользовавшись функцией `localgorage.config( ... )`

#### Использование своих драйверов
Если вас не устраивают хранилища, предлагаемые по умолчанию, вы можете добавить свой драйвер:
``` js Sample code
var myCustomDriver = {
    _driver: 'customDriverUniqueName',
    _initStorage: function(options) {
       // Реализация ...
    },
    clear: function(callback) {
       // Реализация ...
    },
    getItem: function(key, callback) {
       // Реализация ...
    },
    key: function(n, callback) {
       // Реализация ...
    },
    keys: function(callback) {
       // Реализация ...
    },
    length: function(callback) {
       // Реализация ...
    },
    removeItem: function(key, callback) {
       // Реализация ...
    },
    setItem: function(key, value, callback) {
       // Реализация ...
    }
}

// Мы добавляем свой драйвер, после чего мы можем его использовать
localforage.defineDriver(myCustomDriver);
```

#### Заключение
Таким образом localForage является удобной оберткой над хранилищами WebSQL, IndexedDB и localStorage, отделяя работу с хранилищами от непосредственной работой с данными, хранимыми offline. 