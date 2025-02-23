`Concurrent Collections` -- это коллекция, которая допускает одновременный доступ нескольких потоков. Различные потоки обычно могут выполнять итерации по содержимому коллекции, добавлять или удалять элементы. Коллекция отвечает за то, чтобы не допустить своего повреждения.

По умолчанию различные типы `Collection` не являются потокбезопасными. Однако сделать коллекцию потокобезопасной довольно просто
```java
List<String> threadSafeList = Collections.syncronizedList(new ArrayList<String>());
Set<String> threadSafeSet = Collections.syncronizedSet(new HashSet<String>());
Map<String, String> threadSafeMap = Collections.syncronizedMap(new HashMap<String, String>());
```

Когда вы создаете потокобезопасную коллекцию, ==вы никогда не должны обращаться к ней через исходную коллекцию, а только через потокобезопасную обертку== [[Список литературы#^410c7b]]<c. 375>

Начиная с Java 5, а `java.util.collections` появилось несколько новых _потокобезопасных коллекций_, которые не нуждаются в различных методах синхронизации таких, как `Collections.syncronized` [[Список литературы#^410c7b]]<c. 375>
```java
// Начиная с версии Java 5
List<String> threadSafeList = new CopyOnWriteArrayList<String>();
Set<String> threadSafeSet = new ConcurrentHashSet<String>();
Map<String, String> threadSafeMap = new ConcurrentHashMap<String, String>();
```

Хотя ==потокобезопасные коллекции== обеспечивают безопасное добавление или удаление элементов в нескольких потоках, они ==не обязательно обеспечивают безопасную итерацию в одном и том же контексте== (нельзя безопасно итерировать коллекцию в одном потоке, в то время как другой поток изменяет ее, добавляя/удаляя элементы). В этом случае используют Concurrent Collections.
