#####
项目简介：
	1.eureka-server 是eureka的服务端（服务注册中心），端口号是8761
	2.eureka-client 是eureka的客户端（被注册的服务），服务名是service-hi,端口是8762
	3.eureka-client1 是eureka的客户端（被注册的服务），服务名是service-hi,端口号是8763
	4.eureka-service-ribbon 是eureka的客户端（用于负载均衡的调用eureka-client，eureka-client1），也注册到服务注册中心，服务名是service-ribbon,端口号是8764
	5.eureka-service-feign 是eureka的客户端（用于负载均衡的调用eureka-client，eureka-client1），也注册到服务注册中心，服务名是service-ribbon,端口号是8765
	6.eureka-service-feign-hytrix是断路器

#####
服务注册测试流程：
	1.运行eureka-server中的EurekaserverApplication类，打开浏览器观察http://localhost:8761是否启动成功。
	2.运行eureka-client中的ServiceHiApplication类，打开浏览器观察http://localhost:8761是否将service-hi服务注册成功。
	3.输入http://localhost:8762/hi?name=zhangsan 查看显示结果。

#####
客户端 负载均衡ribbon测试：
	1.分别运行eureka-server,eureka-client,eureka-client1,eureka-service-ribbon
	2.访问：http://localhost:8764/hi?name=zhangsan,不断重复访问，浏览器交替显示：

	hi zhangsan,i am from port:8762
	
	hi zhangsan,i am from port:8763

#####
客户端 Feign的测试：
	      当我们使用feign的时候，spring cloud 整和了Ribbon和eureka去提供负载均衡。
	1.分别运行eureka-server,eureka-client,eureka-client1,eureka-service-feign
	2.访问：http://localhost:8765/hi?name=zhangsan,不断重复访问，浏览器交替显示：
	
	hi zhangsan,i am from port:8762
	
	hi zhangsan,i am from port:8763

#####
断路器hystrix在ribbon工程上测试：
	1.启动eureka-server 工程；启动eureka-client工程;
	启动：service-ribbon-hystrix 工程，当我们访问http://localhost:8766/hi?name=zhangsan,浏览器显示：
	
	hi zhangsan,i am from port:8762
	
	此时关闭 eureka-client工程，当我们再访问http://localhost:8766/hi?name=zhangsan，浏览器会显示：
	hi ,zhangsan,sorry,error!
	这就证明断路器起作用了。

#####
断路器hystrxi在feign工程上测试：
	1.启动eureka-server 工程；启动eureka-client工程;
	启动：service-ribbon-hystrix 工程，当我们访问http://localhost:8767/hi?name=zhangsan,浏览器显示：
	
	hi zhangsan,i am from port:8762
	
	此时关闭 eureka-client工程，当我们再访问http://localhost:8766/hi?name=zhangsan，浏览器会显示：
	sorry ,zhangsan
	这就证明断路器起作用了。
	
#####
hystrix-dashboard测试：
	1.启动eureka-server工程，启动eureka-client工程；
	2.打开浏览器：访问http://localhost:8768/hystrix,弹出监控输入页面，
	输入http://localhost:8768/hystrix.stream  . title名字随便取，点击monitor stream，进入下一个界面
	3.新开一个窗口，访问http://localhost:8768/hi?name=zhangsan
	4.观察监控页面的变化。
	
#####
zuul路由、过滤测试：
	1.启动eureka-server、eureka-client、eureka-client1、eureka-service-ribbon、eureka-service-feign工程
	2.访问http://localhost:8769/api-a/hi?name=zhangsannn，页面显示：token is empty!
	  加上token 访问http://localhost:8769/api-a/hi?name=zhangsannn&token=123 ，页面显示 hi zhangsannn,i am from port:8763
	3.访问http://localhost:8769/api-b/hi?name=zhangsannn，页面显示：token is empty!
	  加上token 访问http://localhost:8769/api-b/hi?name=zhangsannn&token=123 ，页面显示 hi zhangsannn,i am from port:8763

