---
layout: post
title: EventSource向浏览器推消息
---

久远以前用JSP+Servlet实现过一个聊天室，使用的是AJAX轮询的方式，来交换服务器和浏览器的数据。但是，轮询的方式在实际的使用中，对于服务器访问过于频繁，并且浏览器发送请求也太频繁，所以上网搜索有没有其他方式。

### **更新浏览器页面常用方式**

* AJAX轮询--------------查询的时间间隔太长或太短都有害处
* WebSocket-------------实现比较复杂，适合双向通行
* EventSource-----------简单方便，适合服务器向浏览器推送给数据

### **EventSouce在W3School上的介绍**

* “EventSource对象是接受服务器发送（Server-Sent）事件通知”
* “Server-Sent事件是指网页自动获取来自服务器的更新”，不需要网页不停的询问是否有新更新

### **简单的例子**

* 页面
    ```html
    
    <html>
    <head>
        <script src="http://cdn.bootcss.com/jquery/3.0.0/jquery.js"></script>
        <script>
                $(document).ready(function(){
                    //初始化EventSource对象
                    var sse = new EventSource("http://127.0.0.1:5000")
                    //绑定Server-Send事件
                    sse.onmessage = function(e){
                        var msg = '<div>' + e.data + '</div>';
                        $(document.body).append(msg);
                    }
                })
        </script>
    </head>
    <body>
    </body>
    </html>

    ```

* 后端消息推送

    ```python
    from flask import Flask
    from flask import Response
    import time
    import random
    import os

    app = Flask(__name__)

    @app.route("/")
    def subscribe():
        def gen():
            while True:
                # 以随机的时间间隔推送消息
                time.sleep(int(random.uniform(1,10)))
                # 生成每次推送的内容
                data = str(os.urandom(10))
                # 格式化为eventsource要求的数据格式，注意添上\n\n
                msg = 'data:{}\n\n'.format(data)
                # 返回msg
                yield msg

        resp = Response(gen(),mimetype="text/event-stream")
        resp.headers['Access-Control-Allow-Origin']= '*'
        return resp

    if __name__ == "__main__":
        app.debug = True
        app.run()
    ```

