---
layout: post
title: "Material Design вместе с Angular.js"
date: 2015-02-07 21:37:30 +0300
comments: true
categories:
           Material Design
           Angular.js
           JavaScript
---

С выходом в свет новой версии Android 5.0, названной Lollipop, компания Google представила новое видение дизайна пользовательского интерфейса - [Google Material Design](http://www.google.com/design/). Вслед за появлением [спецификации](http://www.google.com/design/spec/material-design/introduction.html) разработчики привнесли немного "материальности" в свои фреймворки, такие как Angular.js, React.js, даже всем известный Twitter Bootstrap [сделали "материальным"](https://fezvrasta.github.io/bootstrap-material-design/).

В связи с этим хотелось бы рассмотреть, как же такие известные фреймворки внедряют поддержку Material Design. Рассмотрим это на примере Angular.js.

<!-- more -->
#### Установка
Установка ничем не отличается от установки большинства пакетов - нам достаточно иметь установленный bower:
```bash Sample Code
bower install angular-material
```
Такой способ гораздо предпочтительнее, хотя есть вариант с использованием CDN. С ним мы можем попробовать поэкспериментировать прямо в CodePen или JSFiddle:
```html Sample Code
<head>
  <link rel="stylesheet" href="//ajax.googleapis.com/ajax/libs/angular_material/0.7.0/angular-material.min.css">
</head>
<body>
<!-- Некоторый HTML код -->
  <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.3.6/angular.min.js"></script>
  <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.3.6/angular-animate.min.js"></script>
  <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.3.6/angular-aria.min.js"></script>
  <script src="//ajax.googleapis.com/ajax/libs/angular_material/0.7.0/angular-material.min.js"></script>
</body>
```

#### Разметка
В данном случае разметка построена на современном flexbox'е, что, несомненно, является подспорьем в мире адаптивных сайтов. Фремворк вводит специальные теги и атрибуты, при помощи которых можно построить нужный нам интерфейс. Так, для разметки используется тег `div` с различными атрибутами, например `layout` или `flex`.

#### Компоненты
Фреймворк предлагает к использованию различные компоненты - кнопки, поля ввода, радио-кнопки, вкладки, сайд-бары, подсказки и тому-подобное. В каждый из них мы вникать не будем, мы только рассмотрим общую структуру построения приложения с использованием Material Angular.

#### Что же попробуем создать?
А попробуем мы вот что: мы напишем маленькое, но практичное приложение - простейшую анкету пользователей. Действовать она будет примерно так - две вкладки, в одной поля ввода отдельного человека, во второй вкладке список введенных пользователей.

#### Формируем разметку
Первым делом опишем две вкладки и основу приложения. Приложение мы создаем стандартно - ничего особенного в нем нету:
```javascript Sample Code
var app = angular.module('app', ['ngMaterial']);
app.controller('AppCtrl', function($scope) { });
```
Теперь описываем `div`, к которому будет привязано приложение и контроллер. Внутри него разместим вкладки `md-tabs`, а также `ng-switch`, который будет описывать содержимое каждой вкладки отдельно. Также сделаем привязку к модели `data.selectedIndex`, чтобы иметь возможность контролировать текущую вкладку:
```html Sample Code
<div ng-app="app" ng-controller="AppCtrl">
    <md-tabs md-selected="data.selectedIndex">
        <md-tab id="tab1" aria-controls="tab1-content">
            Ввод анкеты
        </md-tab>
        <md-tab id="tab2" aria-controls="tab2-content">
            Список пользователей
        </md-tab>
    </md-tabs>
    <ng-switch on="data.selectedIndex" class="tabpanel-container">
        <div role="tabpanel" id="tab1-content" aria-labelledby="tab1" ng-switch-when="0">
        </div>
        <div role="tabpanel" id="tab2-content" aria-labelledby="tab2" ng-switch-when="1">
        </div>
    </ng-switch>
</div>
```
Я думаю, вы заметили, что элементы с префиксом `md-` относятся к Material Design, а `ng-` - стандартные элементы Angular.

Теперь опишем поля ввода для первой вкладки. Для этого воспользуемся тегом `md-whiteframe` для создания пустого окна, а в нем разместим непосредственно поля ввода, как на примере ниже:
```html Sample Code
<md-input-container flex>
  <label>Адрес</label>
  <input ng-model="user.address">
</md-input-container>
```
На самом деле кода получается гораздо больше, однако там везде все аналогично - создается контейнер, метка и поле ввода, создается привязка к модели и вуа-ля!

Легко добавить проверку данных. Так, например, мы легко можем добавить ограничение по длине вводимого текста:
```html Sample Code
<input md-maxlength="20">
```
Добавим кнопку сохранения данных:
```html Sample Code
<md-button class="md-fab md-primary" aria-label="Сохранить">
    <md-icon icon="//material.angularjs.org/img/icons/ic_insert_drive_file_24px.svg" style="width: 24px; height: 24px;"></md-icon>
</md-button>
```
Для этой кнопки нам необходимо описать в контроллере функцию `save`, а также добавить прикрепить ее к самой кнопке:
```javascript Sample Code
app.controller('AppCtrl', function($scope){
  $scope.save = function() { }
});
```
```html Sample Code
<md-button ... ng-click="save()"> ...
```

#### Контроллер
Собственно теперь нам необходимо реализовать базовый функционал контроллера `AppCtrl`. Все делается стандартно, ничего неожиданного тут нету. Мой контроллер выглядит примерно так:
```javascript Sample Code
app.controller('AppCtrl', function($scope) {
    // Модели полей ввода
    $scope.user = {
        firstName: '',
        lastName: '',
        address: '',
        state: '',
        postalCode: ''
    };
    // Список введенных и сохраненных анкет
    $scope.users = [];
    // Функция сохранения
    $scope.save = function() {
        $scope.users.push($scope.user);
    }
});
```
#### Вывод списка пользователей
После того, как мы реализовали контроллер на сохранение анкет, нам надо реализовать их вывод. Поддержка "материальных" списков реализуется при помощи тегов `md-list` и `md-item`, а сам Angular предлагает атрибут `ng-repeat`, которым запросто можно пробежать по всем элементам некоторого списка. Реализуем вывод списка:
```html Sample Code
<md-list>
    <md-item ng-repeat="item in users">
        <md-item-content>
            <div class="md-tile-content">
                <h3>{{item.firstName}} {{item.lastName}}</h3>
                <h4>Из города {{item.city}}, область {{item.state}}</h4>
                <p>
                    Адрес: {{item.address}}
                </p>
            </div>
            <md-button class="md-raised md-warn">Удалить</md-button>
        </md-item-content>
    </md-item>
</md-list>
```
Теперь реализуем кнопку удаления отдельного элемента. Material Angular вводит поддержку специальных сервисов, таких как диалоги, подсказки. Реализуем диалог подтверждения удаления пользователя. С этим нам поможет сервис `$mdDialog`, предлагающий несколько вариантов модальных окон. Помимо этого он позволяет нам реализовать свое модальное окно. Здорово, не правда ли? Подключим к кнопке удаления функцию `delete`:
```html Sample Code
<md-button class="md-raised md-warn" ng-click="delete($index, $event)">Удалить</md-button>
```
В функцию будет передаваться индекс выбираемого элемента и событие, которое мы отдадим сервису. Реализуем функцию `delete`. Диалоговое окно создается весьма просто - создается объект и путем последовательного применения нужных команд этот самый объект настраивается:
```javascript Sample Code
$scope.delete = function(index, ev) {
    var confirm = $mdDialog.confirm()
        .title('Вы желаете удалить данного пользователя')
        .content('В случае согласия данные будут полностью удалены из списка.')
        .ok('Удалить!')
        .cancel('Отменить')
        .targetEvent(ev);
    $mdDialog.show(confirm).then(function() {
        $scope.users.splice(index, 1);
    }, function() {});
}
```
После того,как объект был создан, его можно вызвать,указав колбэк-функции, которые будут вызваны в случае нажатия одной из кнопок.
Собственно теперь весь основной функционал реализован и можно заняться внедрением своих "фич" и стилизацией. Однако, если вы хотите, чтобы вся "материальность" была сохранена, то вам стоит почитать официальную спецификацию.

#### Результат
<p data-height="346" data-theme-id="0" data-slug-hash="myMyXx" data-default-tab="result" data-user="asaskevich" class='codepen'>See the Pen <a href='http://codepen.io/asaskevich/pen/myMyXx/'>myMyXx</a> by Alex Saskevich (<a href='http://codepen.io/asaskevich'>@asaskevich</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

#### В завершение
Собственно это все, что я хотел показать, весь код статьи расположен на CodePen вот тут: http://codepen.io/asaskevich/pen/myMyXx. Ну а сам Angular Material Design вместе с примерами и документацией расположен тут: https://material.angularjs.org.

#### Маленькая заметка
Данная статья была первоначально опубликована вот [здесь](http://html5.by/blog/material-design-and-angular-js/).