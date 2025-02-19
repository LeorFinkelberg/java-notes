`@Bean` -- это аннотация уровня метода. Эту аннотацию можно использовать классе вместе с аннотацией `@Configuration`.

Методы с `@Bean` используются для регистрации определения бина в `ApplicationContext` того типа, который задан в качестве возвращаемого значения метода. По умолчанию имя бина совпадает с именем метода
```java
@Configuration
public class AppConfig {
  @Bean
  public TransferServiceImpl transferService() {
    return new TransferServiceImpl();
  }
}
```
