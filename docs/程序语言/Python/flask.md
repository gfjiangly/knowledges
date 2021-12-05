## 记一次诡异的错误

运行Flask web服务，客户端请求一直出现下面的连接错误

```
requests.exceptions.ConnectionError: HTTPConnectionPool(host='10.193.0.20', port=666): Max retries exceeded with url: /detect_hat (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x000001A4FFB50390>: Failed to establish a new connection: [WinError 10061] 由于目标计算机积极拒绝，无法连接。'))
```

L同学要我今晚突然要我和他一起调试一个Web服务，原因是最新他那边请求时不时会返回500错误，需要我这边打断点，看看他那边发来的数据有没有问题。我的服务部署在docker中，666映射到5000端口，因此服务的请求URL类似于

```
http://ip:666/service_name
```

5000是flask服务的默认使用端口，我没修改，采用docker映射这个端口好记一些。L同学要调试，因此我使用了我的本地代码同步到docker上调试，但是我的本地代码flask服务的端口直接使用的是666端口，因为开发环境当时没用docke。必须将主机的666端口映射到docker容器内的666端口，才没有出现上面的连接错误。其实不止端口错了会出现连接错误，未开启服务也会出现连接错误。

## 刷锅

我问L同学这个错误熟不熟，遇没遇到？L同学居然说没遇到！我记忆中对这个错误是有印象的，因为有次我的服务停了，L同学访问了这个服务，然后就遇到了这个连接错误，然后找我的看看，然后我就发现了服务关了，所以他应该比我更熟悉！

## 总结

- 出现了连接错误，不仅要检查IP是否正确，还要检查端口是否正确
- 不仅要检查flask使用的端口是否正确，还要检查是否与docker映射的端口匹配