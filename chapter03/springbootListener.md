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