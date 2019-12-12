<a id="网站性能优化"></a>

## 网站性能优化

* `content`方面
  1.  减少`HTTP`请求：合并文件、`CSS`精灵、`inline Image`
  2.  减少`DNS`查询：`DNS`缓存、将资源分布到恰当数量的主机名
  3.  减少`DOM`元素数量
* `Server`：使用`CDN`、配置`ETag`、开启`Gzip`压缩
* `css`：
  1.  将样式表放到页面顶部
  2.  不使用`CSS`表达式
  3.  使用`<link>`不使用`@import`
* `Javascript`方面
  1.  将脚本放到页面底部
  2.  将`javascript`和`css`从外部引入
  3.  压缩`javascript`和`css`
  4.  删除不需要的脚本
  5.  减少`DOM`操作
* 图片方面
  1.  图片压缩
  2.  优化`css`精灵
  3.  不要在`HTML`中拉伸图片

---

<a id="持续集成"></a>

## 持续集成

GIT + Jenkins

---

<a id="设计模式"></a>

## 设计模式

* 观察者模式（发布-订阅模式）。通过定义依赖关系，当状态发送改变时，订阅者会收到通知；
* 工厂模式。返回一个对象，创建不同的引用类型；
* 构造函数模式。属性绑定到`this`，方法绑定到`prototype`，使用`new`来新增实例；
* 单例模式。产生一个类的唯一实例。

---

<a id="solid原则"></a>

## solid原则

面向对象编程和面向对象设计的五大原则：

* S 单一职责（SRP）：一个类只负责一件事情
* O 开放封闭（OCP）：封闭修改、开放扩展
* L 里式替换（LSP）：一个对象可由其子类代替
* I 接口分离（ISP）：客户不应该被强迫依赖它不实用的方法
* D 依赖倒置（DIP）：抽象不依赖具体，具体依赖于抽象

---

## nginx负载均衡

```t
# 负载均衡服务器列表
# weight 占比几率
upstream baidu {
    server 192.168.0.0.1 weight=1;
    server 192.168.0.0.2 weight=1;
    server 192.168.0.0.3 weight=2;
}
server {
    location / {
        proxy_pass http://baidu;
        proxy_set_header Host $host;
        proxy_set_header X-Forward-For $remote_addr;
    }
}
```

---

<a id="发布订阅与观察者模式"></a>

## 发布订阅与观察者模式

![](asset/pub-sub.png)

* 观察者模式中**观察者和目标直接交互**
* 发布订阅由调度中心进行处理，订阅者和发布者互不干扰

---

<a id="图片懒加载"></a>

## 图片懒加载

* 把真正路径设置在`data-`属性上
* 监听图片是否在可视范围内，如果在就把路径赋值给`src`

---

<a id="移动端高清适配方案"></a>

## 移动端高清适配方案

* 设置`viewport`。`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`
* 字体使用`rem`
* 图片多倍图
* 使用`transform`设处理1像素边框