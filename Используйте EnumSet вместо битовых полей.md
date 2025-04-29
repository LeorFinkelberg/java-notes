Если элементы перечислимого типа используются главным образом в множествах, то традиционно используется схема с применением перечисления `int`, назначающая различные степени 2 каждой константе
```java
// Этот способ устарел!!!
public static final int STYLE_BOLD = 1; // 1 << 2^0
public static final int STYLE_ITALIC = 2; // 1 << 2^1
public static final int STYLE_BOLD = 4; // 1 << 2^2
...
```

Такое представление позволяет использовать побитовую операцию ИЛИ для объединения нескольких констант в множество, известное как _битовое поле_
```java
text.applyStyles(STYLE_BOLD | STYLE_ITALIC);
```

Представления в виде битовых полей позволяют эффективно выполнять операции над множествами, такие как объединение и пересечение, используя побитовую арифметику.

НО лучше использовать класс `EnumSet`. Внутренне каждый `EnumSet` представляет собой _битовый вектор_. Групповые операции, такие как `removeAll` и `retainAll`, реализованы с помощью побитовой арифметики, так как если бы вы работали вручную с битовыми полями. 
```java
// EnumSet - современная альтернатива битовым полям
public class Text {
  public enum Style {
    BOLD,
    ITALIC,
    UNDERLINE,
    ...
    ;
  }

  public void applyStyle(Set<Style> styles) { ... }
}
```

Клиентский код
```java
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```

Здесь метод `applyStyles` принимает `Set<Style>`, а не `EnumSet<Style>`. Хотя представляется вероятным, что все клиенты будут передовать методу именно `EnumSet`, в общем случае хорошая практика заключается в том, чтобы принимать тип интерфейса, а не тип реализации. Это обеспечивает возможность клиенту передать некую другую реализацию `Set`.

Единственный реальный недостаток `EnumSet` -- невозможность создать неизменяемый `EnumSet`. Пока что можно обернуть `EnumSet` в оболочку `Collections.unmodifiableSet`, но при этом пострадают и краткость, и производительность.

Можно имитировать расширяемое перечисление с использованием интерфейса
```java
public interface Operation {
  double apply(double x, double y);
}

public enum BasicOperation implements Operation {
  PLUS("+") {
    public double apply(double x, double y) {
	  return x + y;
    }
  },
  MINUS("-") {
    public double apply(double x, double y) {
	  return x - y;
    }
  },
  ...
  ;
  BasicOperation(String symbol) {
    this.symbol = symbol;
  }
}
```

Имитированное расширение перечисления
```java
public enum ExtendedOperation implements Operation {
  EXP("^") {
    public double apply(double x, double y) {
	  return Math.pow(x, y);
    }
  },
  REMAINDER("%") {
    public double apply(double x, double y) {
	  return x % y;
    }
  },
  ...
  ;
  ExtendedOperation(String symbol) {
    this.symbol = symbol;
  }
}
```

Теперь можно использовать ваши новые операции везде, где можно использовать базовые операции, при условии, что API написаны так, что ==принимают тип интерфейса== (`Operation`), а не реализации (`BasicOperation`).

Здесь не нужно объявлять абстрактный метод `apply` в перечислении, как в нерасширяемом перечислении с реализацией метода, зависимого от конкретного экземпляра. Дело в том, что абстрактный метод (`apply`) является членом интерфейса (`Operation`).

Пример использования расширенных операций
```java
public static void main(String[] args) {
  double x = Double.parseDouble(args[0]);
  double y = Double.parseDouble(args[1]);
  test(ExtendedOperation.class, x, y);
}

private static <T extends Enum<T> & Operation>
void test(Class<T> opEnumType, double x, double y) {
  for (Operation op : opEnumType.getEnumConstants())
    System.out.println("%f %s %f = %f%n", x, op, y, op.apply(x, y));
}
```

Класс литерала служит в качестве _ограниченного токена типа_. Весьма сложное объявление параметра `opEnumType` гарантирует, что объект `Class` представляет как перечисление, так и подтип `Operation`, что в точности является именно тем, что требуется для перебора всех элементов и выполнения операции, связанной с каждым из них.

Другой альтернативой является передача типа `Collection<? extends Operation>`, который является _ограниченным типом с подстановочным символом_
```java
public static void main(String[] args) {
  double x = Double.parseDouble(args[0]);
  double y = Double.parseDoubel(args[1]);
  test(Arrays.asList(ExtendedOperation.values()), x, y);
}

private static void test(Collection<? extends Operation> opSet, double x, double y) {
  for (Operation op : opSet) {
    System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
  }
}
```

Тип аннотации с параметром
```java
import java.lang.annotation.*;
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
  Class<? extends Throwable> value();
}
```

Тип параметра этой аннотации -- `Class<? extends Throwable>`. Этот тип с символом подстановки достаточно громоздский. На простом человеческом языке это означает "объект `Class` для некоторого класса, который расширяет класс `Throwable`" и позволяет пользователю аннотации указать любой тип исключения (или ошибки). 


