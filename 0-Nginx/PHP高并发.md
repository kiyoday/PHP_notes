# PHP高并发

---



##  一、原理

### 减而治之

   - CDN原理
   - nginx限流
   - 异步队列

### 分而治之![image-20200601103538671](C:\Users\12605\AppData\Roaming\Typora\typora-user-images\image-20200601103538671.png)

   - nginx均衡负载

### 特征

- 写 强一致性
- 读 弱一致性

### 难点

- 极致性能的实现
- 高可用的保证

### 核心实现

- 极致性能的读服务实现
- 极致性能的写服务实现
- 极致性能的排队进度查询实现
- 链路流量优化如何做

### 高可用

- 高可用的标准
- 请求链路中每层高可用的实现原理
- 限流、一键降级、自动降级实现





## 二、基础工具及知识

### 1.测压工具的使用

- 安装

   ```bash
   yum -y install httpd-tools
   ab -v #版本号
   ```

- 测试接口最大qps

  ```bash
  ab -n100 -c10 http://xxx 
  #n次数 c并发个数
  	
  #Requests per second: 101.15 [#/sec] (mean)
  ```

### 2.Nginx限流

- #### 策略

1. 按连接数限速,即**并发数**	(ngx.http_limit_conn.module)
2. 按请求速率限速，按照**ip**限制**单位时间内的请求数**	(ngx_http_limit_req_module)

- #### 限流配置

  >  /etc/nginx/nginx. conf目录下	

	> > limit_req_zone $binary_remote_addr zone=mylimit:10m rate=1r/s;//创建规则
	> >  limit_req zone=mylimit burst=1 nodelay; //应用规则









