Ключевое слово `super` играет важную роль на трех ключевых уровнях:
- на уровне конструктора,
- на уровне метода,
- на уровне переменной.
### Уровень конструктора

Ключевое слово `super` используется для вызова конструктора родительского класса. Этот конструктор может быть конструктором по умолчанию или конструктором с аргументами:
- конструктор по умолчанию: `super()`,
- конструктор с аргументами: `super(int no, double amount)`
```java
class ParentClass {
  ParentClass() {
    System.out.println("Конструктор суперкласса");
  }
}

class SubClass extends ParentClass {
  SubClass() {
    // Компилятор неявно добавляет сюда `super()`, то есть в первую строку этого конструктора
    System.out.println("Конструктор подкласса");
  }

  SubClass(int n1) {
    // Компилятор неявно добавлят сюда `super()`, то есть в первую строку этого конструктора
    System.out.println("Конструктор с аргументами");
  }

  void display() {
    System.out.println("Hello");
  }

  public static void main(String[] args) {
    SubClass obj = new SubClass();
	obj.display();
	
	SubClass obj2 = new SubClass(0);
	obj2.display();
  }
}
```
### Уровень метода

Ключевое слово `super` также может использоваться в случае переопределения метода. Ключевое слово `super` может использоваться для вызова метода родительского класса.
```java
class ParentClass {
  // Переопределяемый метод
  void display() {
    System.out.println("Метод родиетльского класса");
  }
}

class SubClass extends ParentClass {
  // Переопределяющий метод
  void display() {
    System.out.println("Метод дочернего класса");
  }

  void printMsg() {
    display(); // вызывается метод дочернего класса
	super.display(); // вызывается метод родительского класса
  }

  public static void main(String[] args) {
    var sc = new SubClass();
    sc.printMsg();
  }
}
```
### Уровень переменной

Ключевое слово `super` используется для обращения непосредственно _к переменной экземпляра родительского класса_. В случае наследования возможно ситуация, когда базовый класс и производный класс могут иметь одинаковые переменные. Для того чтобы различать переменные базового/родительского класса и производного/дочернего класса, в контексте производного класса перед именем переменной базового класса должно стоять ключевое слово `super`
```java
class ParentClass {
  int num = 100;
}

class SubClass exntends ParentClass {
  int num = 10;
  
  void printNumber() {
    System.out.println(num);
	System.out.println(super.num);
  }

  public static void main(String[] args) {
    SubClass obj = new SubClass();
	obj.printNumber();
  }
}
```
