При fluent-стиле программировании вы возвращаете значение `this` из сеттеров, которые в обычном стиле ничего бы невозвращали. Это позволяет выстраивать цепочки вызовов различных методов, что делает код более коротким и удобным для разработчиков [[Список литературы#^410c7b]]<c. 565>
```java
class Person {
  private String name;
  private int age;

  public String getName() {
    return name;
  }

  public int getAge() {
    return age;
  }

  public Person withName(String name) {
    this.name = name;
    return this;
  }

  public Person withAge(int age) {
    this.age = age;
    return age;
  }
}

Person leor = new Person()
  .withName("Leor Finkelberg")
  .withAge(38);
```

Классы, реализующие интерфейс `Iterable`, могут использоваться в циклах `for`. На самом деле это лишь синтаксический сахар ==для получения итератора из объекта и использования его для последовательного получения всех элементов== [[Список литературы#^410c7b]]<c. 577>

