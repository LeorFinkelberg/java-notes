_Итератор_ предлагает универсальный стандартизированный способ доступа к элементам внутри коллекции по одному за раз. Таким образом, итератор предоставляет средства _перечисления содержимого коллекции_. 

В версии JDK 8 добавлен еще один тип итератора, называемый _сплитератором_, или разделяющим итератором. Сплитераторы представляют собой итераторы, которые обеспечивают поддержку параллельной итерации. 

Исключение `ClassCastException` генерируется, когда один объект несовместим с другим, скажем, при попытке добавления в коллекцию несовместимого объекта. Исключение `NullPointerException` возникает, если предпринимается попытка сохранить объект `null`, а элементы `null` в коллекции не разрешены. Исключение `IllegalArgumentException` генерируется в случае применения недопустимого аргумента. Исключение `IllegalStateException` происходит при попытке добавления элемента в заполненную коллекцию фиксированной длины.

Начиная с версии JDK 9, в интерфейсе `List` определен фабричный метод `of()`, который имеет несколько перегруженных версий. Есть версия, которая может принимать переменное количество элементов
```java
static <E> List<E> of(E ... obj)
```
Для всех версий элементы `null` не разрешены.

Двусторонние очереди `Deque` могут функционировать как стандартные очереди "первым пришел -- первым обслужен" (FIFO) или как стеки "последним пришел -- первым обслужен" (LIFO).
### Класс `ArrayList`

Класс `ArrayList` поддерживает _динамические массивы_, которые по мере необходимости могут расти. Стандартные массивы в Java имеют фиксированную длину. После создания массивы не могут увеличиватся или уменьшаться, а потому необходимо заранее знать, сколько элементов будет содержать массив. Но иногда точный размер массива не известен вплоть до времени выполнения. Для ситуаций подобного рода в инфраструктуре Collections Framework предусмотрен класс `ArrayList`. По существу экземпляр `ArrayList` представляет собой массив объектных ссылок переменной длины, то есть он способен динамически увеличиваться или уменьшаться в размерах [[Список литературы#^c58e47]]<c. 667>.

_Списковые массивы_ создаются с начальным размером. В случае превышения этого размера массив автоматически увеличивается, а когда объекты удаляются, размер массива может быть уменьшен
```java
ArrayList<Integer> lst = new ArrayList<Integer>();
lst.add(10);
lst.add(20);
lst.size(); // 2
```

Несмотря на автоматическое увеличение емкости объекта `ArrayList` по ходе сохранения в нем объектов, увеличить емкость объекта `ArrayList` можно вручную, вызвав метод `ensureCapacity()`. Так поступают, когда заранее известно, что в списковом массиве будет находится гораздо больше элементов, чем он может вместить в текущий момент. За счет увеличения его емкости в самом начале удастся предотвратить несколько повторных выделений памяти позже. Поскольку повторные выделения памяти сопряжены с затратами в плане времени, предотвращение ненужных повторных выделений увеличивает производительность
```java
lst.ensureCapacity(10);
```

И наоборот, если необходимо уменьшить размер массива, который лежит в основе объекта `ArrayList`, чтобы он был точно таким же, как количество содержащихся в нем элементов, понадобится вызывать метод `trimToSize()`
```java
lst.trimToSize();
```

Иногда нужно получить фактический массив, который хранит содержимое списка. Для этого можно вызвать метод `toArray()`, определенный в интерфейсе `Collection`. Существует несколько причин преобразования коллекций в массив, например:
- для ускорения обработки определенных операций,
- для передачи массива методу, который не перегружен для приема коллекций,
- для интеграции кода на основе коллекций с унаследованным кодом, который не воспринимает коллекции.
```java
ArrayList<Integer> al = new ArrayList<Integer>();
al.add(10);
al.add(20);
al.add(30);

Integer[] ia = new Integer[al.size()];
ia  // Integer[3] {10, 20, 30}
```

NB! Коллекции могут хранить только ссылки, а не значения примитивных типов. Тем не менее автоупаковка позволяет передавать значение типа `int`  в метод `.add()` без необходимости помещать их внутрь объекта `Integer` в ручную.
### Класс `LinkedList`

Класс `LinkedList` представляет структуру данных типа _связного списка_
```java
LinkedList<String> ll = new LinkedList<>();
ll.add("Java");
ll.add("Python");
ll.get(1); // Python
ll.set(1, ll.get(1).replace("P", "C"));
ll.get(1); // Cython
```
### Класс `HashSet`

Класс `HashSet` создает коллекцию, в которой для хранения данных применяется _хеш-таблица_. При хэшировании информационное содержимое ключа используется для определения уникального значения, которое называется _хеш-кодом_. Впоследствии хеш-код используется в качестве индекса, в котором хранятся данные, связанные с ключом.

Есть форма конструктора, которая принимает емкость (capacity) и коэффициент загрузки (заполнения) хеш-таблицы на основе переданных аргументов. Значение коэффициента загрузки должно находится между 0.0 и 1.0; оно определяет, насколько полной может быть хеш-таблица, прежде чем ее размер будет увеличен. Для конструкторов, не принимающих коэффициент заполнения, используется значение 0.75.

Важно отметить, что класс `HashSet` не гарантирует порядок следования своих элементов, потому что процесс хеширования обычно не позволяет создавать отсортированные множества. Если нужно отсортированное хранилище, тогда лучше выбрать другую коллекцию, например, `TreeSet`.
```java
HashSet<String> langs = new HashSet<>();
langs.add("Python");  // true
langs.add("Java");  // true
langs.add("Python");  // false
```
### Класс `LinkedHashSet`

Класс `LinkedHashSet` поддерживает связный список элементов внутри набора в порядке их добавления, что делает возможной итерацию по набору в этом порядке. Другими словами, при циклическом переборе `LinkedHashSet` с использованием итератора _элементы будут возвращаться в том порядке, в котором они были вставлены_
```java
LinkedHashSep<String> langs = new LinkedHashSet<>();
langs.add("Python");
langs.add("Java");
```
### Класс `TreeSet`

Класс `TreeSet` создает коллекцию, которая в качестве хранилища использует дерево. Объекты хранятся _в отсортированном порядке_ по возрастанию. `TreeSet` это превосходный вариант для организации хранения больших объемов отсортированной  информации, в которой необходим быстрый поиск.
```java
TreeSet<String> lang = new TreeSet<>();
lang.add("Python");
lang.add("Cython");
lang.add("Java");
lang // Cython, Java, Python
```