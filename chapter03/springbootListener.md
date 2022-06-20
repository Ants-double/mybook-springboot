## Spring boot 事件监听  

### 监听启动成功，并打印所有的Bean名称  
``` java

/**
 * @author ants_
 */
@Component
@Slf4j
public class ServiceRunSuccessListener implements ApplicationListener<ApplicationStartedEvent> {
    @Override
    public void onApplicationEvent(ApplicationStartedEvent event) {

        log.info("启动完成，打印beans");
        Arrays.stream(event.getApplicationContext().getBeanDefinitionNames()).forEach(x->
                log.info(x.toLowerCase()));
    }
}
```  
### 监听

``` java
   @Bean
    public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
        return args -> {

            System.out.println("Let's inspect the beans provided by Spring Boot:");

            String[] beanNames = ctx.getBeanDefinitionNames();
            Arrays.sort(beanNames);
            for (String beanName : beanNames) {
                System.out.println(beanName);
            }

        };
    }
```