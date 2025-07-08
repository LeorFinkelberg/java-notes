В простейшем случае файл `build.gradle` должен иметь следующее содержание (для примера приводится только одна зависимость JOL)
```java
// build.gradle

plugins {
	id 'java'
}

repositories {
	mavenCentral()
}

dependencies {
	implementation("org.openjdk.jol:jol-core:0.17")
}
```
и располагаться в корне проекта, например
```bash
/ваш_проект
  ├── build.gradle    <-- Здесь добавляем зависимость
  ├── settings.gradle
  ├── src/
  └── gradle/
```
Устанавливаем утилиту `gradle`, например так
```bash
$ curl -s "https://get.sdkman.io" | bash  # устанавливаем SDKMAN
$ sdk install gradle 8.14.3  # устанавливаем gradle
```
Теперь можно установить Gradle Wrapper (`gradlew`) в проекте; для этого выполняем следующую команду в корне проекта (там же, где лежит `build.gradle`)
```bash
$ gradle -v  # 8.14.3
$ gradle wrapper --gradle-version 8.14.3
```
В результате в текущей директории появятся:
- `gradle/wrapper/gradle-wrapper.jar`, 
- `gradle/wrapper/gradle-wrapper.properties`,
- `gradlew`,
- `gradlew.bat`

После установки враппера, все команды нужно выполнять через `gradlew`. Например, можно обновить зависимости проекта
```bash
$ ./gradlew build --refresh-dependencies
```

Для того, чтобы приложение можно было запускать с помощью `./gradlew` , нужно добавить `application`
```kotlin
// build.gradle

plugins {
	id 'java'
	id 'application'  // NB!
}

application {  // NB!
	mainClass = 'solver.Solver'
}

jar {
	manifest {
		attributes 'Main-Class': 'solver.Solver'
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation("org.openjdk.jol:jol-core:0.17")
}
```
Структура проекта следующая 
```bash
# mip_solver/
.
├── build.gradle
└── src
    └── main
        └── java
            └── solver
                └── Solver.java
```
Приложение можно запустить так
```bash
$ ./gradlew run
```

Чтобы запустить сборку проекта с помощью `gradle`, следует перейти в директорию проекта и вызвать утилиту `gradlew`
```bash
$ ./gradlew --configure-on-demand -x check run
```