Использование традиционного оператора `switch` для уровня приоритета на основе кода события
```java
class TraditionalSwitch {
  public static void main(String[] args) {
    int priorityLevel;
    int eventCode = 6010;

    switch (eventCode) {
      // в традиционном операторе switch используется укладка операторов case
      case 1000:
      case 1205:
      case 8900:
	      priorityLevel = 1;
	      break;
      case 2000:
      case 6010:
      case 9128:
         priorityLevel = 2;
         break;
      case 1002:
      case 7023:
      case 9300:
         priorityLevel = 3;
         break;
      default:
         priorityLevel = 3;
    }
    // code = 6010; level = 2
    System.out.println("code = " + eventCode + "; level = " + priorityLevel);
  }
}
```

В прошлом, когда две константы обрабатывались одной и той же кодовой последовательностью, использовалась _укладка операторов_ `case`. Укладка операторов `case` позволяет всем трем конструкциям `case` использовать одну и ту же кодовую последовательность, предназначенную для установки переменной `priorityLevel` в 1.

Более элегантное решение предусматривает применение списка констант `case`. Начиная с JDK 14, в одном операторе `case` можно указывать более одной константы `case`, просто разделяя их запятыми.
```java
case 1000, 1205, 8900:  // список констант
  priorityLevel = 1;
  break;
```

Предоставить значение выражения `switch` можно с помощью нового оператора `yield`, который имеет следующий вид
```java
yield value;
```

Здесь значение `value` производится оператором `switch` и может быть любым выражением, совместимым с требуемым типом значения. Оператор `yield` немедленно завершает `switch`.

Важно отметить, что `yield` -- контекстно-чувствительное ключевое слово, то есть `yield` за рамками выражения `switch` является просто идентификатором без специального смысла.

Выражение `switch`
```java
int x = switch(y) { // ...
```

Каждый путь через выражение `switch` должен обеспечивать результат.
```java
class SwitchExpr {
  int eventCode = 6010;

  int priorityLevel = switch (eventCode) {
    case 1000, 1205, 8900:
      yield 1;
    case 2000, 6010, 9128:
      yield 2;
    case 1002, 7023, 9300:
      yiled 3;
    default:
      yield 0;
  }
};
```

Поскольку данный `switch` участвует в операторе присваивания, он обязан заканчиваться точкой с запятой.

Выражение `switch` обязано быть исчерпывающим. То есть операторы `case` должны охватывать все значения, которые могут возникать.

Пример оператора `case` со стрелкой
```java
boolean stopNow;
...

int priorityLevel = switch (eventCode) {
  case 1000, 1205, 8900 -> {
    stopNow = false;
    yield 1;
  }
  case 2000, 6010, 9128 -> {
    stopNow = false;
    yield 2;
  }
  ...
  default -> {
    stopNow = false;
    yield 0;
  }
}
```

Начиная с JDK 16 используются записи для того чтобы можно было вернуть из `switch` сразу несколько значений.

Оператор `case` со стрелкой также можно применять в операторе `switch` , что позволяет получать операторы `switch`, в которых не может происходить сквозной проход через `case`. По существу он работает во многом подобно традиционному оператору `switch`, но без сквозного прохода
```java
class StatementSwitchWithArrows {
  public static void main(String[] args) {
    int up = 0;
    int down = 0;
    int left = 0;
    int right = 0;
    char direction = 'R';

    switch (direction) {
      case 'L' -> {
        System.out.println("...");
        left++;
      }
      case 'R' -> {
        Sysytem.out.println("...");
        right++;
      }
      ...
    }
  }
}
```

NB! Выражения `switch` ==обязаны== быть исчерпывающими, но это не касается операторов `switch` [[Список литературы]]<c. 513>

Пример 
```java
class Vowels {
  public static void main(String[] args) {
    boolean yIsVowel = true;
    char ch = 'Y';
    boolean isVowel = switch (ch) {
      case 'a', 'e', 'i', ... -> true;
      case 'y', 'Y' -> { if (yIsVowel) yield true; else yield false; }
      default -> false;
    }
  }
}
```

Пример _записи_ (доступна, начиная с JDK 16) -- узконаправленного, специализированного класса
```java
...
record Employee(
  String name,
  int idNum
) {}

Employee emp = new Employee("Doe, John", 1047);

System.out.println(emp.name()); // вызывается метод на чтение
System.out.println(emp.idNum()); // вызывается метод на чтение
```

В Python можно было написать что-то вроде
```python
import typing as t

class Employee(t.NamedTuple):
    name: str
    id_num: int

emp = Employee("Doe, John", 1047)
print(emp.name)
print(emp.id_num)
```

С записью связан один ключевой момент: ее данные хранятся в закрытых финальных полях и предоставляются только методы получения [[Список литературы#^c58e47]]<c. 521>. Следовательно, содержащиеся в записи данные являются неизменяемыми. После конструирования записи ее содержимое изменять нельзя. Тем не менее, если запись содержит ссылку на какой-то объект, тогда в этот объект можно вносить изменения, но не разрешено изменять то, на что ссылается ссылка в записи. Таким образом, в терминах Java говорят, что записи _поверхностно неизменяемы_. 

Пример канонического конструктора
```java
record Employee(String name, int idNum) {
  public Employee(String name, intn idNum) {
    this.name = name.trim();
    this.idNum = idNum;
  }
}
```

Пример компактной формы канонического конструктора
```java
...
public Employee {
  name = name.trim();
}
```
