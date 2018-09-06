## SpringMVC的工作流程

### SpringMVC的流程
SpringMVC 是一种基于Servlet的技术，提供了核心控制器DispatcherServlet和相关的组件。
* 首先SpringMVC围绕着DispatcherServlet工作的，DispatcherServlet继承自Servlet
用以处理Http请求。
* 在Servlet初始化即调用init方法的时候，SpringMVC会根据配置，获取配置信息，从而得到
URL和Handler之间的映射关系HandlerMapping。
* 为了更加灵活和增强功能，SpringMVC还会给处理器加入拦截器，所以可以在处理器执行前后加入
自己的代码，这样就构成了一个处理器的执行链，并且根据上下文初始化视图解析器等内容；
* 当处理器返回的时候就可以通过视图解析器(ViewResolver)定位视图，然后将数据模型渲染到
视图中，用以响应用户的请求。
#### SpringMVC处理请求的过程
1. 当一个请求到来时，DispatcherServlet首先通过请求和事先解析好的HandlerMapping，找到
对应的处理器(Handler)，这样就是准备开始运行处理器和拦截器组成的执行链HandlerExecutionChain；
2. 运行handler需要一个对应的环境，于是通过Handler对应的适配器HandlerAdapter, 通过
这个适配器就能运行其对应的处理器以及拦截器；
3. 处理器会将模型和视图返回给DispatcherServlet，DispatcherServlet会将对应的视图信息
传递给视图解析器(ViewResolver)。注意这一步取决于是否使用逻辑视图，如果是逻辑视图，就不会进行处理
而是直接通过视图渲染数据模型。

### SpringMVC的核心组件
