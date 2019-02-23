# Spring Web MVC的架构
## Servlet 
### 特点
* 请求/响应式(Request/Response)
* 屏蔽网络通讯细节，由Tomcat等容器进行处理
* 完整生命周期

### 职责
* 处理请求
* 资源管理，数据库，内存，缓存等
* 视图渲染

总体而言Spring MVC 是一种前端控制器模式。

## Spring MVC
### Spring Framework 时代
* 实现Controller
* 配置Web MVC组件
* 部署DispatcherServlet