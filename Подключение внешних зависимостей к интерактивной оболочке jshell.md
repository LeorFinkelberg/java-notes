Если требуется функциональность какой-то внешней библиотеки, то подключить ее в `jshell` можно следующими способами (источник [здесь](https://stackoverflow.com/questions/43111018/how-to-import-external-libraries-in-jshell-java-9)):
- Можно установить переменную окружения `CLASSPATH` , предварительно скачав JAR-файл библиотеки в `/opt/libs/` (для примера, пусть будут библиотеки Guava [`guava-33.4.0-jre.jar`](https://repo1.maven.org/maven2/com/google/guava/guava/33.4.0-jre/guava-33.4.0-jre.jar), [jackson-databind](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/2.1.4/jackson-databind-2.1.4.jar) и [`commons-langs3-3.13.0.jar`](https://repo.mavenlibs.com/maven/org/apache/commons/commons-lang3/3.13.0/commons-lang3-3.13.0.jar?utm_source=mavenlibs.com))
NB! Jar-файл (Java Object Layout) можно найти здесь  https://mvnrepository.com/artifact/org.openjdk.jol/jol-core 
```bash
# gradle
// https://mvnrepository.com/artifact/org.openjdk.jol/jol-core
implementation("org.openjdk.jol:jol-core:0.17")  
```
а примеры работы с библиотекой JOL здесь https://www.baeldung.com/java-memory-layout 
```java
>>> import org.openjdk.jol.vm.VM;
>>> import org.openjdk.jol.info.ClassLayout;


>>> int[] nums = {10, 20, 30};
>>> VM.current().sizeOf(nums); // 32 байта

>>> class SimpleInt {
  private int state;
}

>>> System.out.println(ClassLayout.parseClass(SimpleInt.class).toPrintable());
REPL.$JShell$17$SimpleInt object internals:
OFF  SZ   TYPE DESCRIPTION               VALUE
  0   8        (object header: mark)     N/A
  8   4        (object header: class)    N/A
 12   4    int SimpleInt.state           N/A
Instance size: 16 bytes
Space losses: 0 bytes internal + 0 bytes external = 0 bytes total
```

Примеры использования в библиотек в оболочке jshell
```bash
$ export CLASSPATH=/opt/libs/commons-lang3-3.13.0.jar:/opt/libs/guava-33.4.0-jre.jar
```
- Можно воспользоваться параметром `--class-path` оболочки `jshell`
```bash
$ jshell --class-path /opt/libs/commons-lang3-3.13.0.jar:/opt/libs/guava-33.4.0-jre.jar
```
- Либо можно воспользоваться командой `/env`
```bash
jshell> /env -class-path /opt/libs/commons-lang3-3.13.0.jar:/opt/libs/guava-33.4.0-jre.jar
```

После чего в сессии уже можно импортировать нужные библиотеки
```java
jshell> import com.google.common.collect.Lists;
jshell> import org.apache.commons.lang3.StringUtils;
jshell> import org.apache.commons.lang3.ArrayUtils;
jshell> /env -class-path /opt/libs/commons-lang3-3.13.0.jar
jshell> StringUtils.countMatches("One fish, two fish, three fish", "fish"); // 3
jshell> int[] getSomething() {
            return ArrayUtils.EMPTY_INT_ARRAY;
        }
...
```