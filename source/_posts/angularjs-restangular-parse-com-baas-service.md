title: 'Parse.com REST API 搭配 Restangular'
date: 2014-08-04 00:14:16
tags:
- AngularJS
- parse.com
- rest
---


這幾天想搞一個簡單的應用，想說體驗一下 BaaS 的服務...看看是不是真的這麼方便易用？耳聞 Parse.com API 做得不錯，又被 Facebook 收購。想必應該品質有保證？讓我們繼續看下去...

<!-- more -->

對了，我選擇 Parse 的原因之一就是他有一個漂亮的後台，`Analytics`, `Data Browser`...集成的 Dashboard！這對於一個實驗性 Prototype 的產品來說，不用花精神去額外寫一個後台可以省去不少時間！(當然，phpmyadmin 也不是不行啦...)


JavaScript SDK or REST API
--------------------------
回歸正題，由於我是用 AngularJS 所以一開始就面臨兩個抉擇：

1. JavaScript SDK
2. REST API

首先我先試了 [JavaScript SDK](https://www.parse.com/docs/js_guide) (真是個錯誤的開始...)

文件其實還算詳細，Parse.Object 主要是透過 Backbone 的 model/collection 來操控，如果之前有接觸擴 [Backbone.js](http://backbonejs.org)，我想 JS SDK 應該非常適合你使用，前提是不要跟 AngularJS 搭在一起...

AngularJS 的 data-binding 是透過例如說

```js
var model = {
  name: "zack",
  age: 18
};
```

不需要像 backbone's style 假如我要取得 model 的 properties 要使用 getter `model.get("name")`，雖然可以透過 `.toJSON()` 轉換但是就無法 **two-way binding**...

總之這個搭配十分有問題阿~ (AngularJS + Parse.com JS SDK)；在官方論壇上有看到幾個 JS SDK 跟 AngularJS 搭配的範例，總覺得還是很髒很麻煩！有興趣可以研究看看

1. [parse-angular-demo](https://github.com/brandid/parse-angular-demo)
2. [Integrating Parse Data with ng-model in AngularJS](http://www.jonathanbroquist.com/integrating-parse-data-with-ng-model-in-angularjs/)

ps. 第二個作者他自己也說他沒用這種方式了

於是在我花了一點時間把玩了 JS SDK with AngularJS 後，決定奔向 REST API 懷抱，不要用他的 JS SDK 了。

Restangular with REST API
-------------------------

在 AngularJS 中要存取 RESTful-like 的 API 除了直接用 `$resource` 外，大概就是用阿根廷的開發者 [Martin Gontovnikas](https://github.com/mgonto) 的 [Restangular](https://github.com/mgonto/restangular) 了吧！

> AngularJS service to handle Rest API Restful Resources properly and easily

於是我開始串 REST API，發現還真的是滿方便的！

Parse 的 REST API 需要在每次送出 request 的時候在 header 中夾帶你的資料，因此我們可以在 AngularJS 中的設定如下

```js
angular.module('app', ['restangular'/*, others*/])
.config(function(RestangularProvider /*, others*/) {
  RestangularProvider.setBaseUrl('https://api.parse.com/1/classes/');
  RestangularProvider.setDefaultHeaders({
    "X-Parse-Application-Id": "YOUR_APPLICATION_ID",
    "X-Parse-REST-API-Key": "YOUR_REST_API_KEY"
  });
  RestangularProvider.setRestangularFields({id: "objectId"});
};
```

透過上述設定，Restangular 就會在每次發送 request 的時候幫你把 header 設定好！

一開始我們要先設定 API Base Url，`RestangularProvider.setBaseUrl('https://api.parse.com/1/classes/');`，將來若 API 版本或是 endpoint 有變只要更改此處即可。

再來可以看到下面有一行 `setRestangularFields({id: "objectId"});` ，這一行是因為 Restangular 預設將取回來的物件中的 `id` 這個屬性拿出來將來若你要針對這個物件做 `PUT/UPDATE/DELETE` 等動作的時候，它會自動幫你從原本的 `/collection` 變成 `/collection/{id}`。

然而，Parse 的物件並不是存成 `id` 而是叫做 `objectId` 因此我們必須在此手動設定 `{id: "objectId"}`

Examples
--------

透過一些程式碼應該可以比較快速上手

取得一個 Object
===============
**one(route, id)**

```js
var id = 1;
var Event = Restangular.one('Events', id);

Event.get({include: 'brand'}).then(function(event) {
  $scope.event = event;
});
```

由於先前有設定過 Base API 所以這邊的 `route = 'Events'` 實際上是會對 `https://api.parse.com/1/classes/Events` 做存取。Restangular 有使用 **promise** 所以我們可以使用 `.then()` 來執行取得資料後的操作。在上面程式碼當中，取得了 objectId 為 1 的 Event 並且指定給 `$scope.event`

其中 `.get({include: 'brand'})` 則表示送出的 param 為 `Events?include=brand`。這邊目的是希望我們要連帶取得 Event 底下的 brand 資料，此部分請參考 Parse 的 REST API Document。

新增一個 Object
===============
**all(route)**: This will create a new Restangular object that is just a pointer to a list of elements for the specified path.
**post(subElement, elementToPost, [queryParams, headers])**


```js

var Comments = Restangular.all('Comments');

var event = {
  name: 'test',
  objectId: 1
};

var comment = {
  name: 'nickname',
  conetnt: 'test comment'
};

Comments.post({
  event: { // 此處是新增一個 Pointer 類型的 Event 資料 (詳見 API DOC)
    __type: 'Pointer',
    className: 'Events',
    objectId: event.objectId
  },
  name: comment.name,
  content: comment.content
}).then(function() {
  console.log('success'); // 成功
}, function(error) {
  console.log(error); // 失敗
});
```

可以看到 `Restangular.all('Comments')` 就代表 `API_BASE + '/' + 'Comments'` 有所有的 Comments 集合。

在上面的程式當中，我想要新增一個 Class 為 `Comment` 的物件，並且將它其中一個 Pointer 欄位指向某一個 `Event`。

`.post` 完之後會返回一個 Promise 可以接續後續的流程。


以上，我想就可以輕鬆使用 AngularJS + Restangular CRUD 在 Parse.com 平台啦！



