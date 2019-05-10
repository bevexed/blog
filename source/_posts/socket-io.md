---
title: socket.io
date: 2019-02-15 13:46:52
categories:
- Node
tags:
- Node
- 插件
---
## 安装
```bash
> npm install socket.io -S
```

## 使用
### 客户端
```javascript
import io from 'socket.io-client'

// 得到与服务器的链接对象
const socket = io('ws://localhost:9000');

// 接收消息
socket.on('receiveMsg', function (data) {
  console.log('浏览器接收消息');
})

// 发送消息
socket.emit('sendMsg', {name: '12', data: Date.now()})
console.log('发送消息');

```

### 服务端
```javascript
module.exports = function (server) {
	const io = require('socket.io')(server);
	// 监视客户端与服务器端的链接
	io.on('connection',function (socket) {
		console.log('链接成功');
		socket.on('sendMsg',function (data) {
			console.log('服务器接受到浏览器消息');
			// 全局发数据
			io.emit('receiveMsg',data.name+'_'+data.data)
			// 单一发数据
			socket.emit('receiveMsg',data.name+'_'+data.data)
			console.log('服务器向浏览器发消息');
		})
	})
}

// www
require('../socketIO/test')(server);
```

## 参考网站
- https://socket.io/get-started/chat/
- https://blog.csdn.net/neuq_zxy/article/details/77531126
