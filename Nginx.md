# 什么是Nginx
Nginx是一个高性能的HTTP和反向代理服务器，特点是占用内存少，并发能力强。

Nginx可以作为静态页面的web服务器，Nginx专为性能优化而开发，可以支持高达50000个并发连接数。

# 正向代理和反向代理

## 概念
正向代理：在客户端和目标服务器之间有同一个代理服务器，为了从目标服务器获取资源（国外资源），客户端（国内）无法直接访问服务器资源（国外），使用代理服务器，客户端把请求发给代理服务器（不会暴露客户端ip），通过代理去访问外部资源（接收的是代理的ip）。

反向代理：代理服务器接收客户端的请求，并将其转发到一个或者多个后端服务器，客户端只知道请求被发送到Nginx的地址，而不知道实际处理请求的服务器地址。

# 负载均衡

 ## 概念
 负载均衡：通过将请求分发到多台服务器上，从而实现分布式处理，让多台服务器一起来处理客户端的请求，避免一台服务器的负载过大，从而出现故障影响整个系统的运行。

 ## 负载均衡算法
 1.Round Robin（轮询）：默认的负载均衡算法，每个请求按照顺序依次分发给后端服务器，实现简单且公平。
 实例：
 http {
  upstream backend {
    server backend1.example.com;
    server backend2.example.com;
  }

  2.Least Connections（最少连接数）：将请求分配给活跃连接数最少的服务器，以平衡请求的负载。适用于处理请求耗时不等的情况。
  实列：
  http {
  upstream backend {
    least_conn;
    server backend1.example.com;
    server backend2.example.com;
  }

  3.IP Hash（基于客户端IP的哈希）：根据客户端的IP地址进行哈希，使同一个客户端的请求会被分配到同一台服务器上面，这样解决服务器session的问题（保持session）会话。
  实例：
  http {
  upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
  }

  4.Weighted Round Robin（加权轮询）：根据服务器的权重分配请求，权重越高的服务器获取的请求越多。
  实例：
  http {
  upstream backend {
    server backend1.example.com weight=2;
    server backend2.example.com weight=1;
  }  

  5.URL Hash（基于URL的Hash）：根据请求的URL进行哈希，使相同URL的请求分配到同一台服务器上。适用于需要保持缓存一致性的应用。
  实例：
  http {
  upstream backend {
    hash $request_uri consistent;
    server backend1.example.com;
    server backend2.example.com;
  }

  6.Fair（按连接响应时间分配）：根据服务器的响应时间分配请求，响应时间短的服务器获取到的请求更多。适用于响应时间相差较大的情况。
  实例：
  http {
  upstream backend {
    least_time header connect;
    server backend1.example.com;
    server backend2.example.com;
  }

# 动静分离
为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力。

# server块

## 概念
在Nginx配置文件中，server块是一个用来自定义虚拟主机的配置块。一个server块代表了一个服务器配置。

实例：
server {
                listen 80;
                server_name easypan.wuhancoder.com;
                charset utf-8;
				
								
				 location / { 
                       alias E:/webser/web_app/easypan-front/dist/;
                       try_files $uri $uri/ /index.html;
                       index  index.html index.htm;
                 }

                  location /api { 
                              proxy_pass http://localhost:7090/api;
                              proxy_set_header x-forwarded-for  $remote_addr;
                 }
				


}
## 参数
listen：监听HTTP端口的请求。
server_name：指定该虚拟主机的域名，当客户端发起请求时，会根据本地hosts进行IP和域名的映射。
charset：设置字符集，在响应中正确处理和传输Unicode字符。
location：匹配请求URL路径。
alias：指定一个路径的别名。
try_files $uri $uri/ /index.html;尝试匹配请求的文件，如果文件存在则返回该文件，否则转到下一个匹配规则。最后，将请求转发到 index.html 文件。这主要用于处理单页应用（SPA）的路由，确保对不存在的静态资源文件返回主页。
index index.html index.htm;：指定默认的索引文件名，如果请求的路径是以 / 结尾，则会在该路径下寻找指定的索引文件（如 index.html）。
proxy_pass：Nginx会将匹配到的请求转发给该后端服务器进行处理。
