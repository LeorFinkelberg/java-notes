Метод для проверки объекта на равенство `null`
```java
Object nullableObject = methodReturnObject();
Objects.isNull(nullableObject);
```

Метод для проверки объекта на неравенство `null`
```java
Object nullableObject = methodReturnObject();
Objects.nonNull(nullableObject);
```

Применить какой-то метод только объектам отличным от `null` можно так
```java
List<Object> someObjects = methodGetList();
someObjects.stream()
  .filter(Objects::nonNull)
  .forEach(this::doSomething);
```