# Spring MVC 组件
-- 它们分别是处理器映射器（HandlerMapping),处理器适配器(HandlerAdapter),视图解析器(ViewResolver)

## 1.处理器映射器
### 1.1 作用
通过处理器映射器，根据请求的URL、method 等信息可以将Web请求映射到正确的处理器Controller（handler)上。当接收到请求时，DispatcherServlet将请求交给HandlerMapping处理器映射，让它检查请求并找到一个合适的HandlerExecutionChain,这个HandlerExecutionChain包含一个能处理该请求的处理器Controller。 然后DispatcherServlet执行在HandlerExecutionChain中处理器Conntroller.
---------
Spring 内置了许多处理器映射器策略，目前最主要由三个实现。SimpleUrlHandlerMapping,BeanNameHandlerMapping,RequestMappingHandlerMapping.
tip: Spring MVC3.1之前使用的DefaultAnnotaionHandlerMapping,Spring MVC3.1之后改为RequestMappingHandlerMapping.

## 2. 处理器适配器
### 2.1 作用
 根据映射器找到的处理器Handler(controller)信息，按照特定的规则执行相关的处理。此时如果成功获得HandlerAdapter后，将开始执行拦截器的preHandler(...)方法。
 提取Request中的模型数据，填充Handler入参，在填写入参的过程中，根据你的配置，Spring会做一些额外的工作：
 HttpMessageConveter 消息参数转换，比如把JSON，xml转为对象;String转为Double 或者数字、日期的格式化，以及验证数据的有效性。