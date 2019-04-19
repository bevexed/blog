---
title: indexedDB
date: 2019-04-02 18:18:51
categories: HTML5
tags:
---
## 特征
### NoSQL 数据库
- 不支持 SQL 查询语句
- 不属于 关系型数据库

### 键值对存储
- 采用对象仓库（object store）存放数据
- 所有数据都可以直接存入，包括 JavaScript对象
- 每一条数据都有 独一无二的 主键对应，重复会报错

### 异步
- 不会锁死浏览器

### 支持事务（transaction）
- 只要一步失败，整个事务取消，数据库回滚到事务发生之前的状态

### 同源限制
- 每一个数据库都有一个与之对应的域名
- 不能跨域访问数据库

### 存储空间大
- 一般 不小于250M

### 支持二进制存储
- 可以存储 ArrayBuffer对象 和 Blob对象

## 基本概念
- 数据库：IDBDatabase 对象
- 对象仓库：IDBObjectStore 对象
- 索引： IDBIndex 对象
- 事务： IDBTransaction 对象
- 操作请求：IDBRequest 对象
- 指针：IDBCursor 对象
- 主键集合：IDBKeyRange 对象

### 数据库
- 数据库是一些列相关数据的容器。
- 每个域名（协议+域名+端口）都可以创建任意多个数据库
- IndexedDB 数据库有版本的概念,同一时间只能有一个版本的数据库存在。
- 修改数据库结构（新增或删除表、索引或者主键），只能通过升级数据库版本完成。

### 对象仓库
- 每个数据库包涵若干个对象仓库，类似于关系数据库中的表格。

### 索引
- 为了加快数据检索，在对象仓库里，为不同属性建立索引。

### 事务
- 数据记录的读写和删除，倒要通过事务完成。
- 事务对象提供 error、abort、complete 三个事件，用来监听操作结果。

### 数据记录
- 对象仓库保存的是数据记录。
- 每条记录类似于关系型数据库的行，但是只有主键和数据体两个部分。
- 主键用来建立默认的索引，必须是不同的，否则会报错
  - 主键可以是数据记录里面的一个属性，也可以是一个递增的整数编号

## 操作
### 打开数据库 `indexedDB.open()`
- 参数1：数据库名字
  - 指定数据库不存在，新建数据库
  
- 参数2：数据库版本
  - 新建数据库版本默认为 1
    
```javascript
const request = window.indexedDB.open('databaseName', version);
```

- 返回一个 IDBRequest 对象
  - 该对象通过 error 、 success 、 upgradeneeded 三种事件处理打开数据库的操作结果
#### error
```javascript
request.onerror = function (event) {
  console.log('数据库打开报错', event);
};
```

#### success
- 通过request对象的result属性拿到数据库对象。

```javascript
let db;

request.onsuccess = function (event) {
  db = request.result;
  console.log('数据库打开成功');
};
```

#### upgradeneeded
- 当指定的版本号大于 实际操作的版本号，触发该事件。
- 通过事件对象的target.result属性，拿到数据库实例。
```javascript
let db;

request.onupgradeneeded = function (event) {
  db = event.target.result;
}
```

### 新建数据库
- 新建数据库和打开数据库是同一操作
- 打开数据库后的后续操作主要在 upgradeneeded 中操作

#### 新建对象仓库 createObjectStore()
- 参数1：表格名字
  
- 参数2：主键配置
  - { autoIncrement: true } // IndexedDB 自动生成主键。
  - { keyPath: 'id' } // 自定义主键为 'id'

```javascript
let db
request.onupgradeneeded = function(event) {
  db = event.target.result;
  let objectStore;
  // 检测表格是否 存在
   if (!db.objectStoreNames.contains('person')) {
     // 新建一张叫做 person 的表格 ，主键是 id
     objectStore = db.createObjectStore('person', { keyPath: 'id' });
   }
}
```

#### 创建索引 createIndex()
- 参数1：索引名称

- 参数2：索引所在属性

- 参数1：配置对象
  - { unique: false } // 该属性是否包涵重复的值
```javascript
request.onupgradeneeded = function(event) {
  let db = event.target.result;
  let objectStore = db.createObjectStore('person', { keyPath: 'id' });
  objectStore.createIndex('name', 'name', { unique: false });
  objectStore.createIndex('email', 'email', { unique: true });
}
```

### 新增数据
- 向对象仓库写入数据记录
- 需要通过新建事务完成
  - 新建时必须指定 表格名城 和 操作模式（‘只读’、‘读写’）
- 新建事务后 通过 IDBTransaction.objectStore(name) 方法，拿到 IDBObjectStore 对象
- 通过 add() 方法向表格 写入数据
- 写入操作是一个 异步操作，通过监听链接对象的 `success` 和 `error` 事件，判断是否写入成功

```javascript
function add() {
  let request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .add({ id: 1, name: '张三', age: 24, email: 'zhangsan@example.com' });

  request.onsuccess = function (event) {
    console.log('数据写入成功', event);
  };

  request.onerror = function (event) {
    console.log('数据写入失败', event);
  }
}
```

### 读取数据
- 读取数据也通过事务完成
```javascript
function read() {
   let transaction = db.transaction(['person']);
   let objectStore = transaction.objectStore('person');
   let request = objectStore.get(1);

   request.onerror = function(event) {
     console.log('事务失败');
   };

   request.onsuccess = function(event) {
      if (request.result) {
        console.log('Name: ' + request.result.name);
        console.log('Age: ' + request.result.age);
        console.log('Email: ' + request.result.email);
      } else {
        console.log('未获得数据记录');
      }
   };
}
```

### 遍历数据 openCursor()
- 遍历数据表格的所有记录，要使用指针对象 IDBCursor
- openCursor() 方法是一个异步操作，需要监听 success 事件返回的结果
```javascript
function readAll() {
  let objectStore = db.transaction('person').objectStore('person');

   objectStore.openCursor().onsuccess = function (event) {
     let cursor = event.target.result;

     if (cursor) {
       console.log('Id: ' + cursor.key);
       console.log('Name: ' + cursor.value.name);
       console.log('Age: ' + cursor.value.age);
       console.log('Email: ' + cursor.value.email);
       cursor.continue();
    } else {
      console.log('没有更多数据了！');
    }
  };
}
```

### 更新数据 IDBObject.put()
```javascript
function update() {
  let request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .put({ id: 1, name: '李四', age: 35, email: 'lisi@example.com' });

  request.onsuccess = function (event) {
    console.log('数据更新成功');
  };

  request.onerror = function (event) {
    console.log('数据更新失败');
  }
}
```

### 删除数据 openCursor()
```javascript
function remove() {
  let request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .delete(1);

  request.onsuccess = function (event) {
    console.log('数据删除成功');
  };
}
```

### 使用索引
- 索引的意义在于，可以让你搜索任意字段，也就是说从任意字段拿到数据记录

```javascript
objectStore.createIndex('name', 'name', { unique: false });

let transaction = db.transaction(['person'], 'readonly');
let store = transaction.objectStore('person');
let index = store.index('name');
let request = index.get('李四');

request.onsuccess = function (e) {
  let result = e.target.result;
  if (result) {
    // ...
  } else {
    // ...
  }
}
```

## indexedDB 对象
- 浏览器提供的原生对象

### indexedDB.open()
- 打开数据库
- 异步操作
- 立即返回一个 IDBOpenDBRequest 对象

```javascript
// 打开一个名为 test 版本为 1 的数据库，如果数据库不存在则创建该数据库
let openRequest = window.indexedDB.open('test', 1);
```
#### 参数
- 参数1：数据库名称
  - 格式为字符串，不可省略

- 参数2：数据库版本
  - 大于 0 的正整数 ，0 会报错
  - 该参数大于当前版本，会触发数据库升级
  - 该参数可以省略，如果数据库已存在，打开最新版本数据库；如果数据库不存在，创建该版本的数据库，默认版本为 1
  
#### 回调事件
- success：打开成功
- error：打开失败
- upgradeneeded：第一次打开该数据库，或数据库版本发生变化
- blocked：上一次的数据库连接还未关闭
> 第一次打开数据库，会先触发 upgradeneeded 事件，然后触发 success 事件
> success 事件发生后，从 openRequest.result 属性可以拿到已经打开的 IndexedDB 数据库对象 
```javascript
let openRequest = indexedDB.open('test', 1);
let db;

openRequest.onupgradeneeded = function (e) {
  console.log('Upgrading...');
}

openRequest.onsuccess = function (e) {
  console.log('Success!');
  db = openRequest.result;
}

openRequest.onerror = function (e) {
  console.log('Error');
  console.log(e);
}
```

### indexedDB.deleteDatabase()
- 用于删除一个数据库
- 立即返回一个 IDBOpenDBRequest 对象，然后对数据库执行异步删除
- 删除不存在的数据库时，不会报错
- 当调用该方法后，当前数据的其他已经打开的连接都会接收到 versionchange 事件

#### 参数
- 数据库名字

#### 回调事件
- success：删除成功
- error：删除失败
```javascript
let DBDeleteRequest = window.indexedDB.deleteDatabase('demo');

DBDeleteRequest.onerror = function (event) {
  console.log('Error');
};

DBDeleteRequest.onsuccess = function (event) {
  console.log('success');
};
```

### indexedDB，cmp()
- 比较两个值是否为 indexedDB 的相同的主键。
- 返回一个整数，表示比较的结果
  - 0 ：相同 
  - 1： 第一个主键大于第二个主键
  - -1： 第一个主键小于第二个主键
```javascript
window.indexedDB.cmp(1, 2); // -1
// 注意，这个方法不能用来比较任意的 JavaScript 值。如果参数是布尔值或对象，它会报错。
window.indexedDB.cmp(1, true); // 报错
window.indexedDB.cmp({}, {}) // 报错
```

## IDBRequest 对象
- 表示打开的数据库连接
- indexedDB.open() 和 indexedDB.deleteDatabase() 方法会返回这个对象
- 数据库的操作都是通过此对象完成
- 对象的所有操作都是 `异步` 完成
  - 通过 readyState 属性判断是否完成
    - pending：正在进行
    - done：完成，有可能失败也有可成功
- 操作完成，触发 error 或 success 事件
  - 可以通过 result 属性 和 error 属性 拿到操作结果
    - 如果在 pending 状态去读取这两个属性的值会报错
### 属性
- IDBRequest.readyState：等于pending表示操作正在进行，等于done表示操作正在完成。
- IDBRequest.result：返回请求的结果。如果请求失败、结果不可用，读取该属性会报错。
- IDBRequest.error：请求失败时，返回错误对象。
- IDBRequest.source：返回请求的来源（比如索引对象或 ObjectStore）。
- IDBRequest.transaction：返回当前请求正在进行的事务，如果不包含事务，返回null。
- IDBRequest.onsuccess：指定success事件的监听函数。
- IDBRequest.onerror：指定error事件的监听函数。







## 插件
### level.js

