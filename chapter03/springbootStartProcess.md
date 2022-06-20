# Springboot 2.6.8 启动流程

## 构造SpringApplication
1. 把参数source设置到SpringApplication属性中，这个source可以是任何类型的参数。接受传入的Resource参数，但是不能为空  
2. 执行Web类型推断技术，确认服务类型  
3. 创建并初始化BootstrapRegistryInitializer 设置到bootstrapRegistryInitializers中  
4. 创建并初始化ApplicationContextInitializer 设置到Initializers属性中  
5. 创建并初始化ApplicationListener 设置到Listeners属性中  
5. 推断确认Main方法  
-- 与2.4之前多了创建并初始化BootstrapRegistryInitializer
``` java
	public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
		this.resourceLoader = resourceLoader;
		Assert.notNull(primarySources, "PrimarySources must not be null");
		this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
		this.webApplicationType = WebApplicationType.deduceFromClasspath();
		this.bootstrapRegistryInitializers = new ArrayList<>(
				getSpringFactoriesInstances(BootstrapRegistryInitializer.class));
		setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
		this.mainApplicationClass = deduceMainApplicationClass();
	}
```





## 执行SpringApplication的run方法
0. 构造计时器，用来记录Springboot启动所用的时间。
1. 创建BootStrapContext
2. 初始化监听器，获取BootStrapContext配置SpringApplicationRunListener并开启监听，用于监听run方法的执行。  
3. 创建并初始化ApplicationArguments,获取run方法传递的args参数。准备环境变量参数，并整合args参数到Environment类中 发布环境初始化完成事件。  
4. 创建初始化ConfigurableEnvironment,封装main方法的参数，初始化参数，写入到Environment中，发布ApplicaionEnivronmentPrepapreEvent事件，参数绑定后返回Environment。  
5. 打印banner和版本。  
6. 构造Spring容器，ApplicationContext上下文，先填充Environment环境和设置参数，如果application有设置beanNameGenerator（bean),resourceLoader就注入到上下文中，调用初始化的切面，发布ApplicationContextInitializedEvent事件。  
7. SpringApplicationRunListeners发布finish事件。  
8. 停止计时，日志打印总共启动的时间  
9. 发布Springboot程序启动事件ApplicationStrarted  
10. 调用ApplicationRunner和CommandLineRunner接口。  
11. 最后发布ApplicationReadEvent事件，标志着Springboot启动完成，可以处理接收请求。   
-- 添加了创建BootStrapContext
``` java
public ConfigurableApplicationContext run(String... args) {
		long startTime = System.nanoTime();
		DefaultBootstrapContext bootstrapContext = createBootstrapContext();
		ConfigurableApplicationContext context = null;
		configureHeadlessProperty();
		SpringApplicationRunListeners listeners = getRunListeners(args);
		listeners.starting(bootstrapContext, this.mainApplicationClass);
		try {
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
			ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);
			configureIgnoreBeanInfo(environment);
			Banner printedBanner = printBanner(environment);
			context = createApplicationContext();
			context.setApplicationStartup(this.applicationStartup);
			prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
			refreshContext(context);
			afterRefresh(context, applicationArguments);
			Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
			}
			listeners.started(context, timeTakenToStartup);
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, listeners);
			throw new IllegalStateException(ex);
		}
		try {
			Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
			listeners.ready(context, timeTakenToReady);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
```