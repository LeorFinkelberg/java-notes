Если требуется функциональность какой-то внешней библиотеки, то подключить ее в `jshell` можно следующими способами (источник [здесь](https://stackoverflow.com/questions/43111018/how-to-import-external-libraries-in-jshell-java-9)):
- Можно установить переменную окружения `CLASSPATH` , предварительно скачав JAR-файл библиотеки в `/opt/libs/` (для примера, пусть будут библиотеки Guava [`guava-33.4.0-jre.jar`](https://repo1.maven.org/maven2/com/google/guava/guava/33.4.0-jre/guava-33.4.0-jre.jar), [jackson-databind](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/2.1.4/jackson-databind-2.1.4.jar) и [`commons-langs3-3.13.0.jar`](https://repo.mavenlibs.com/maven/org/apache/commons/commons-lang3/3.13.0/commons-lang3-3.13.0.jar?utm_source=mavenlibs.com))
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
jshell> StringUtils.countMatches("One fish, two fish, three fish", "fish"); // 3
```