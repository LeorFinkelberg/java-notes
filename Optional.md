`Optional` -- это объект-контейнер, который может содержать или не содержать значение `null`. Если значение присутствует, то функция `isPresent()` вернет `true`, а функция `.get()` вернет значение.

Предусмотрены дополнительные методы, зависящие от наличия содержащегося значения, такие как `orElse()`, который возвращает значение по умолчанию, если значение отсутствует, и `ifPresent()`, выполняющий блок кода, если значение присутствует.

NB! Используйте метод `.map()` в `Optional` для работы со значениями, которые могут быть равны `null`, не выполняя при этом явных проверок на `null` [[Список литературы#^410c7b]]<c. 390>

```java
String value = "java";
Optional.ofNullable(value).map(String::toUpperCase).orElse("default"); // java

String value = null;
Optional.ofNullable(value).map(String::toUpperCase).orElse("default"); // default
```

Пример
```java
String value = Optional.ofNullable(foo)
  .map(Foo::getBar)
  .map(Bar::getBaz)
  .map(Baz::toString)
  .orElse("");
```

Не используйте `Optional.get()`, так как это может привести к выбросу исключения `NoSuchElementException`. Методы `Optional.orElse(T)` и `Optional.orElseGet(Supplier<? extends T>)` предоставляют возможность задать значение по умолчанию в случае, если `Optional` пуст.
```java
String getDefaultValue() {
  return "defaultValue";
}

Optional.ofNullable(value).orElse("defaultValue"); // ТОЛЬКО для констант!!!
Optional.ofNullable(value).orElseGet(() -> getDefaultValue());
```

NB! Принципиальная разница между методами `orElse` и `orElseGet` заключается в том, что последний выполняется только в том случае, если `Optional` пуст, в то время как функция, переданная в виде аргумента первому методу, выполняется даже в том случае, если `Optional` не пуст. ==Поэтому `orElse` следует использовать только для констант и ни в коем случае не для предоставления значения, основанного на каких-либо вычислениях== [[Список литературы#^410c7b]]<c. 393>

Если требуется поднять исключение, то можно воспользоваться методом `.orElseThrow()`
```java
Optional.of("test").orElseThrow(IllegalArgumentException::new);
```

Синтаксис лямбда-выражения можно использовать и в том случае, если необходимо выбросить исключение с сообщением
```java
Optional.empty().orElseThrow(() -> new IllegalArgumentException("My message ..."));
```

Метод `.filter()` используется для указания на то, что вы хотите получить значение только в том случае, если оно соответствует вашему условию
```java
String value = null;
Optional.ofNullable(value)
  .filter(elem -> Objects.equals(elem, "test"))
  .isPresent(); // false
```

Обертки-контейнеры `OptionalDouble`, `OptionalInt` и `OptionalLong` работают подобно `Optional`, но специально предназначены для обертывания примитивных типов
```java
int value = 42;
OptionalInt value = OptionalInt.of(value);
```

Пустые контейнеры могут быть проверены с помощью `.isPresent()`. Еще можно так `OptionalInt.of(value).ifPresent(System.out::println)`.

В Java все _примитивы передаются по значению_. _Объекты передаются по ссылке_, что означает, что указатель на объект передается в качестве параметра любым методам, которые их принимают [[Список литературы#^410c7b]]<c. 396>