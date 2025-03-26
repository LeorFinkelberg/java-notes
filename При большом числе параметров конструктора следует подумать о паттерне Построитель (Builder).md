Вместо того, чтобы создавать объект непосредственно, клиент вызывает конструктор (или статическую фабрику) со всеми необходимыми параметрами и получает _объект построителя_. Наконец, клиент вызвает метод `build` параметров для создания объекта (обычно неизменяемого). Построитель обычно представляет собой статический класс-член класса, который он строит [[Список литературы#^0e3edb]]<c. 38>

==NB! Значения по умолчанию задаются в статическом билдере==
```java
// Паттерн проектирования Построитель

public class MyClass {
  private final int servingSize;
  private final int servings;
  private final int calories;
  ...

  public static class Builder {
    // Необходимые параметры
    private final int servingSize;
	private final int servings;

    // Необязательные параметры -- инициализированные значениями по умолчанию
    private int calories = 0;
    private int fat = 0;
    ...

    public Builder(int servingSize, int servings) {
	  this.servingSize = servingSize;
	  this.servings = servings;
    }

    public Builder calories(int val) {
	  calories = val;
	  return this;
    }

    public Builder fat(int val) {
	  fat = val;
	  return this;
    }
	...

    public MyClass build() {
	  return MyClass(this);
    }
  }

  private MyClass(Buidler builder) {
    servingSize = builder.servingSize;
    servings = builder.servings;
	calories = builder.calories;
	...
  }
}
```

Код клиента выглядит так
```java
MyClass myClass = new MyClass.Builder(240, 8)
    .calories(100)
    .sodium(35)
    .build();
```

Шаблон Построитель хорошо подходит для иерархии классов. Используйте параллельные иерархии строителей, в которых каждый вложен в соответствующий класс. _Абстрактные классы имеют абстрактных строителей. Конкретные классы имеют конкретных строителей_
```java
public abstract class Pizza {
  public enum Topping { HAM, MUSHROOM, ...};
  final Set<Topping> toppings;
  
  abstract static class Builder<T extends Builder<T>> {
    EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
	
    public T addTopping(Topping topping) {
	  toppings.add(Objects.requireNonNull(topping));
	  return self();
    }
  }

  abstract Pizza build();

  // Подклассы должны перекрывать этот метод, возвращая "себя"
  protected abstract T self();

  Pizza(Builder<?> builder) {
    toppings = builder.toppings.clone();
  }
}
```

`Pizza.Builder` является _обобщенным типом с рекурсивным параметром типа_. Это, наряду с абстрактным методом `self()`, обеспечивает корректную работу цепочек методов в подклассах без необходимости приведения типов. Этот обходной путь для того факта, что в Java нет "типа самого себя", известен как идиома _имитации собственного типа_.

Шаблон проектирования Построитель является хорошим выбором при проектировании классов, конструкторы или статические фабрики которых будут иметь большое количество параметров, особенно если многие из этих параметров оказываются необязательными или имеют одинаковый тип.