---
layout: post
title: "Как сделать простое мобильное приложение с помощью Apache Cordova и AngularJS"
date: 2016-01-06 10:50:21 +0200
comments: true
categories: 
- cordova
- angularjs
- mobile
- android
- nodejs
---
<p>Все мои планы на долгие рождественские выходные полетели коту под хвост из-за погоды. Сначала снег, а я на летней резине. Потом оттепель, гололед, лужи, дождь, туман, все разом - от одного взгляда за окно хочется еще глубже залезть под одеяло. Вместо того, чтобы слоняться по дому в тоске, я решила попробовать то, чего никогда не делала - написать мобильное приложение и поставить его на свой android-смартфон. А в случае успеха написать небольшой гайд для таких же новичков, как я.</p>

<p>Cordova - это open-sourse проект, позволяющий создавать программы для мобильных устройств, используя стандартные инструменты веб-разработчика: HTML, CSS и Javascript. По сути, мы просто пишем сайт, а потом запускаем в консоли волшебную команду, и она превращает его в приложение для выбранных платформ. Документация в подробностях здесь - <a href="https://cordova.apache.org" target="_blank">www.cordova.apache.org</a>, и я постараюсь ее не пересказывать, а сосредоточиться  на руководстве к действию.</p>

<!-- more -->

<p>Вот что должно получиться в результате:</p>

<img src="/images/listimg.png" alt="shopList app" />

<p>Я решила сделать простую программку для ведения списков - будь то список продуктов для похода в магазин или to-do list, потому что все имеющиеся в google play бесплатные программы угнетают меня своим дизайном или обилием ненужных функций.</p>

<p>Описание установки рабочего окружение актуально для OS X и других unix систем, но не думаю, что на Windows порядок действий будет радикально отличаться.</p>

<p>Итак, предположим, что Node.js и git у Вас уже установлены. Ставим Cordova:</p>

``` 
npm install -g cordova
```
<p>Затем, чтобы запускать эмулятор и делать билд приложения, нужно установить всю требуху для выбранной платформы - iOS, android, windows или еще какой-нибудь. Полный список поддерживаемых платформ и фич <a href="https://cordova.apache.org/docs/en/latest/guide/support/index.html" target="_blank">все там же</a></p>

<p>Моя цель - android, поэтому поставить пришлось следующее:</p>

<ul>
<li>Java</li>
<li>Java SE Development Kit</li>
<li>Android SDK</li>
</ul>

<p>Теперь можно создавать проект, точно так же, как описано в официальной документации:</p>

``` 
cordova create hello com.example.hello HelloWorld
cd hello
cordova platform add android
cordova platform add browser

```

<p>И запускаем билд.</p>

``` 
cordova build
```

<p>Если все пройдет гладко, вы увидите в терминале многабукаф и надпись "BUILD SUCCESSFUL" в самом конце.</p>

<p>Я, помимо андроида, добавила в список платформ браузер, потому что для меня отлаживать разметку и css так гораздо удобнее, чем каждый раз перезапускать эмулятор или ставить новую версию приложения на физическое устройство.</p>

<p>Кстати, чуть не забыла - если до этого Вы не запускали андроид-эмулятор, то нужно будет его создать, но это просто, сообщение об ошибке в командной строке подскажет вам, как.</p>

<p>Запускаем только что созданный шаблон приложения в эмуляторе:</p>

``` 
cordova emulate android
```

<p>Или в браузере:</p>

``` 
cordova run browser
```
<p>Или устанавливаем и запускаем на живом устройстве:</p>
 
``` 
cordova run android
```

<p>Перед нами дефолтная болванка программы с логотипом Кордовы и надписью "device is ready". Все дальнейшие изменения нужно производить в каталоге www. При запуске любой из указанных выше трех команд или </p>

``` 
cordova build
```

<p>Cordova сама сгенерирует нам правильный код и сложит его в папку platforms, в которой мы пока что ничего не трогаем.</p>

<p>Чтобы сделать задачу более интересной, я решила использовать AngularJS. Этот фреймворк сейчас в моде, значит надо попробовать и понять, почему все так любят его, а не какой-нибудь Backbone.js.</p>

<p>В этом случае нам здорово облегчит жизнь <a href="http://ngcordova.com/" target="_blank">ngCordova</a>. Это прослойка между angular и плагинами cordova, предоставляющая тонну возможностей в минимуме кода.</p>

<p>Открываем index.html в папке www и подключаем скрипты:</p>

``` html index.html
        <!-- собственно ангуляр -->
        <script type="text/javascript" src="js/angular.min.js"></script>
        <!-- библиотека для событий touch и swipe на сенсорных девайсах -->
        <script type="text/javascript" src="js/angular-touch.min.js"></script>
        <!-- ngCordova должна быть подключена после ангуляра, но до самой Cordova-->
        <script type="text/javascript" src="js/ng-cordova.js"></script>
        <!-- Cordova.js был по дефолту -->
        <script type="text/javascript" src="cordova.js"></script>
        <!-- файл контроллера для нашего приложения -->
        <script type="text/javascript" src="js/controllers.js"></script>
        <!-- дефолтный index.js - предлагаю его пока не удалять, а просто закомментить его содержимое, мало ли, вдруг пригодится -->
        <script type="text/javascript" src="js/index.js"></script>
```

<p>Дальше самое простое - разметка. Нам нужен заголовок, список и текстовое поле с кнопкой. Да здравствует минимализм! </p>

```  html index.html
    <body>
        <div class="app">
            <h1>Shop List</h1>
            <ul class="list">
                <li>
                    //тут будет содержимое списка
                </li>
                <li ng-bind="new_item" ng-show="new_item"></li>
            </ul>
            <div class="add-item">
                <input type="text" value="" />
                <div class="button" value="">Add</div>
            </div>
        </div>
    </body>           
```

<p>Теперь очередь контроллера. Записываем в него массив с нашим списком покупок (пока что статично) и функцию добавления нового элемента в список.</p>

``` js controllers.js

var shopList = angular.module('shopList', ['ngCordova', 'ngTouch']);

shopList.controller('ItemsListCtrl', function ($scope) {
	$scope.items = [
		{"name": "Bread", "qty": 1, "status": false, "priority": 0},
		{"name": "Fruits", "qty": 1, "status": false, "priority": 0},
		{"name": "Water", "qty": 1, "status": false, "priority": 0}
	
	];
});

    $scope.add = function(name) {

        var itemObj = {
            'name': name,
            'qty': 1,
            'status': false,
            'priority': 0
        };
        if ($scope.items) {
            $scope.items.push(itemObj);
        } else {
            $scope.items = [];
            $scope.items.push(itemObj);

        }

        $scope.new_item = "";
    };
```

<p>Вносим соответствующие изменения в шаблон - добавляем контроллер к тегу body, ng-repeat из массива к li, привязываем к текстовому полю модель new_item, а к кнопке - функцию добавления новой записи.</p>

{% raw %}
``` html 
    <body ng-app="shopList">
        <div class="app" ng-controller="ItemsListCtrl">
            <h1>Shop List</h1>
            <ul class="list">
                <li ng-repeat="item in items track by $index">
                    {{item.name}}
                </li>
                <li ng-bind="new_item" ng-show="new_item"></li>
            </ul>
            <div class="add-item">
                <input type="text" value="" ng-model="new_item" />
                <div class="button" value="" ng-click="add(new_item)">Add</div>
            </div>
        </div>
   </body>
 
```
{% endraw %}

<p>На css подробно останавливаться не буду. В конце статьи есть ссылка на репозиторий с кодом, можно использовать css-файл оттуда или написать самостоятельно.</p>

<p>Если сейчас вы запустите команду</p>

``` 
cordova run browser
```
<p>или </p>

``` 
cordova run android
```
<p>то приложение будет работать - добавлять запись в список. Но если его перезапустить, новые данные не сохранятся. Поэтому надо записывать их в какое-нибудь постоянное хранилище. </p>

<p>Официальная документация предлагает несколько <a href="https://cordova.apache.org/docs/en/4.0.0/cordova/storage/storage.html" target="_blank">вариантов</a>. Я воспользуюсь двумя, которые мне кажутся самыми простыми - localStorage и запись в файл.</p>

<p>Для начала рассмотрим localStorage - это легче всего, не нужны никакие дополнительные плагины, просто сохраняем массив с объектами в строку и записываем. Нужно добавить следующий код в конец функции add:</p>

``` js controllers.js

        var data = JSON.stringify($scope.items);
        //Save to localStorage
        localStorage.setItem('data', data);
        
```

<p>И в самое начало контроллера, вместо статичного массива:</p>

``` js controllers.js

shopList.controller('ItemsListCtrl', function ($scope, $cordovaFile) {
    //Load from localStorage
    $scope.items = JSON.parse(localStorage['data']);
    
    ...
}
```

<p>Можно оставить так или же записывать наши данные в файл. Я выбираю второй вариант. Для него потребуется использовать модуль <a href="http://ngcordova.com/docs/plugins/file/" target="_blank">File</a> из ngCordova.</p>

<p>Удаляем (или комментируем) код про localStorage, и вместо него добавляем вот это:</p>

``` js controllers.js

shopList.controller('ItemsListCtrl', function ($scope, $cordovaFile) {

   document.addEventListener("deviceready", function () {
        $scope.listPath = cordova.file.externalRootDirectory;
        //Read from file
        $cordovaFile.readAsText($scope.listPath, "list.json").then(function(result) {
            $scope.items = JSON.parse(result);
        }, function(err) {
            alert('Error! ' + err);
        });
    }, false);
    
    ...
}

```

<p>Этот плагин будет читать информацию из файла и в случае успеха возвращать строку, которую необходимо сконвертировать в объект для дальнейшего использования, а в случае ошибки - выводить алерт с ее описанием. Но чтобы что-то прочитать, нужно сначала что-то записать! Добавляем функцию writeFile.</p>

``` js controllers.js

    $scope.writeFile = function(){
        var data = JSON.stringify($scope.items);
        $cordovaFile.writeFile($scope.listPath, "list.json", data, true).then(function(result) {
        }, function(err) {
            alert('Error! ' + err);
        });
    };

```

<p>И используем ее в функции add вместо записи в localStorage:</p>

``` js controllers.js

    $scope.add = function(name) {
        var itemObj = {
            'name': name,
            'qty': 1,
            'status': false,
            'priority': 0
        };
        if ($scope.items) {
            $scope.items.push(itemObj);
        } else {
            $scope.items = [];
            $scope.items.push(itemObj);

        }

        $scope.new_item = "";

        var data = JSON.stringify($scope.items);
        //Save to file
        $scope.writeFile();
    };
    
```

<p>Вот и все, теперь все данные записаны в файле. Я записала их в externalRootDirectory, просто потому что мне так захотелось (а еще потому, что так при переустановке приложения во время разработки файл не перезаписывается), но правильнее было бы использовать applicationStorageDirectory или externalApplicationStorageDirectory, чтобы не увеличивать энтропию во вселенной.</p>

<p>Теперь мы можем добавлять пункты в список, и они точно никуда не денутся, потому что возможности удаления в программе пока не предусмотренно :) Но это легко исправить. Как лучше реализовать удаление пункта меню в нашем минималистичном интерфейсе? Сделать кнопку? Нет, так мне не нравится, лучше добавить возможность вывода меню по долгому нажатию на пункт списка. Пока что в этом меню будет только одна опция - "Delete", но впоследствии можно будет добавить туда что-нибудь еще.</p>

<p>Реализуем это с помощью директивы, которая будет ловить событие долгого нажатия. Я не писала ее сама, а взяла отсюда: <a href="https://gist.github.com/BobNisco/9885852" target="_blank">https://gist.github.com/BobNisco/9885852</a></p>

``` js controllers.js

var shopList = angular.module('shopList', ['ngCordova', 'ngTouch']).
directive('onLongPress', function($timeout) {
        return {
            restrict: 'A',
            link: function($scope, $elm, $attrs) {
                $elm.bind('touchstart', function(evt) {
                    // Locally scoped variable that will keep track of the long press
                    $scope.longPress = true;

                    // We'll set a timeout for 600 ms for a long press
                    $timeout(function() {
                        if ($scope.longPress) {
                            // If the touchend event hasn't fired,
                            // apply the function given in on the element's on-long-press attribute
                            $scope.$apply(function() {
                                $scope.$eval($attrs.onLongPress)
                            });
                        }
                    }, 600);
                });

                $elm.bind('touchend', function(evt) {
                    // Prevent the onLongPress event from firing
                    $scope.longPress = false;
                    // If there is an on-touch-end function attached to this element, apply it
                    if ($attrs.onTouchEnd) {
                        $scope.$apply(function() {
                            $scope.$eval($attrs.onTouchEnd)
                        });
                    }
                });
            }
        };
});

```

<p>Так же, как в примере, добавляем обработчик события:</p>

``` js controllers.js

    $scope.itemOnLongPress = function(index) {
        $scope.items[index].menu = true;
    };

```

<p>С его помощью а также благодаря встроенной директиве ng-show в шаблоне можно изменять видимость контекстного меню для каждого пункта.</p>

{% raw %}

``` html index.html
<li ng-repeat="item in items track by $index" on-long-press="itemOnLongPress($index)">
	{{item.name}}
	<div class="item-menu" ng-show="item.menu" ng-init="item.menu = false">
		<ul>
			<li ng-click="delete($index)">Remove</li>
		</ul>
	</div>
</li>

```
{% endraw %}

<p>Внимательные могли заметить, что я уже добавила вызов функции delete, теперь надо написать саму эту функцию, которая будет удалять указанный объект из массива и перезаписывать файл. Параметр $index обозначает порядковый номер элемента в массиве, начиная с нуля.</p>


``` js controllers.js

    $scope.delete = function(index) {
        $scope.items.splice(index, 1);
        $scope.writeFile();
    };

```

<p>Ну вот, теперь можно и добавлять, и удалять записи списка. Но чего-то не хватает... Я думаю, нужно как-то маркировать статус записи - выполнено/не выполнено. Я использую для этого иконки из FontAwesome - icon-check для пункта, помеченного выполненным, и icon-check-empty для пустого чекбокса.</p>

<p>Меняем разметку:</p>

``` html index.html

<li ng-repeat="item in items track by $index" on-long-press="itemOnLongPress($index)" on-touch-end="itemOnTouchEnd($index)">
	<i ng-class="{'icon-check-empty': !item.status, 'icon-check': item.status}" ng-click="changeStatus($index)"></i>
	{{item.name}}
... 
</li>


```

<p>И добавляем функцию для переключения статуса:</p>

``` js controllers.js

    $scope.changeStatus = function(index) {
        $scope.items[index].status = !$scope.items[index].status;
        $scope.writeFile();
    };

```
<p>Теперь сделанные дела и купленные продукты можно пометить галочкой, все как у тру-девелоперов, хаха :) </p>

<p>Итак, у нас есть законченное приложение, которое выполняет свои функции, и ушло на его создание всего несколько часов (вкуривание документации в эти часы тоже входит). Писала пост об этом я гораздо дольше, чем делала само приложение! </p>

<p>Теперь можно подключить свой телефон, включить отладку по USB, установить приложение и наслаждаться своим могуществом)</p>

<p>Полный код здесь: <a href="https://github.com/rthewheel/shopList/" target="_blank">https://github.com/rthewheel/shopList/</a>

Но он, возможно, будет изменяться.</p>



