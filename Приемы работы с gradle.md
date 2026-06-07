## Установка gradle

Установить `gradle` можно например через SDK, но важно, что глобальная установка gradle это неидеальное решение. Разработчики часто собирают несколько разных проектов так, чтобы каждый из них использовал собственную версию Gradle. 

Чтобы это устроить, Gradle вводит понятия _обертки_ (wrapper). Задача `gradle wrapper` фиксирует в проекте конкретную версию Gradle, к которой затем можно обращаться командой `./gradlew`. Обертки `gradlew` считаются хорошей практикой, потому что они помогают избежать несовместимости версий, так что вам редко придется выполнять команду `gradle` напрямую [[Список литературы#^eb6c16]]<c. 443>.

В простейшем случае файл `build.gradle` должен иметь следующее содержание (для примера приводится только одна зависимость JOL)
==NB! `build.gradle.kts` обязательно должен располагаться в корне проекта==
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
	implementation("org.apache.commons:commons-lang3:3.18.0")
}
```
==и располагаться в корне проекта==, например
```bash
/ваш_проект
  ├── build.gradle.kts    <-- Здесь добавляем зависимость
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
$ gradle wrapper
$ gradle wrapper --version 8.14.3
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
	mainClass = 'solver.Solver' // путь отсчитывается от src/main/java
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

В случае `build.gradle.kts`, для запуска приложения через `./gradlew` 
```bash
plugins {  
    id("java")  
    application  // <- NB!
}  
  
group = "org.example"  
version = "1.0-SNAPSHOT"  
  
application {  
    mainClass.set("org.example.Main")  // <- NB!
}  
  
repositories {  
    mavenCentral()  
}  
  
dependencies {  
    // Apache Commons Lang 3  
    implementation("org.apache.commons:commons-lang3:3.18.0")  
    // JUnit Test  
    testImplementation(platform("org.junit:junit-bom:5.10.0"))  
    testImplementation("org.junit.jupiter:junit-jupiter")  
}  
  
tasks.test {  
    useJUnitPlatform()  
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
## Задачи 

Ключевым понятием Gradle является задача (task): это единица работы, которую можно запустить на выполнение. Задачи могут зависеть от других задач, их можно настраивать с помощью сценариев, а также добавлять через систему плагинов Gradle. Задачи напоминают цели Maven, хотя на концептуальном уровне они больше похожи на функции. У задач есть четко определенный ввод и вывод, их можно комбинировать и выстраивать цепочки. 

Gradle предоставляет превосходные средства интроспекции, и важнейшее из них -- метазадача `./gradlew tasks`, которая выводит список текущих доступных задач в проекте. 

Если запустить любую задачу с флагом `--dry-run`, выводится список задач, которые Gradle выполнил бы, но сами задачи не выполняются. 
### Сценарии сборки

Центральный компонент сборки Gradle -- _сценарий сборки_ (buildscript). В Gradle сценарий сборки представляет собой исполняемый код на языке программирования, который часто называется предметно-ориентированным языком (Domain-Specific Language, DSL). Современные версии Gradle поддерживают Groovy и Kotlin.

NB! Сценарии сборки, написанные на Kotlin, используют расширение `.gradle.kts` вместо `.gradle`.

В терминологии Gradle задача `assemble` компилирует и упаковывает файл JAR. При пробном запуске (с ключом `--dry-run`) выводятся все этапы, часть из которых отсутствует в списке задача по умолчанию
```bash
./gradlew assemble --dry-run
```

Кэш сборок позволяет повторно использовать результаты задач из более ранних сборок или даже из сборок, которые выполнялись в других расположениях. Эта возможность включается в проекте с помощью флага командной строки `--build-cache`
```bash
./gradlew clean build --build-cache
```

В Gradle 7 были удалены некоторые давно устаревшие конфигурации, например `compile` и `runtime`. В наше время следует переходить на более новые варианте -- `implementation` (или `api`) и `runtimeOnly`.

В Gradle встроены мощные средства интроспекции, с помощью которых можно "заглянуть под копот"
```bash
./gradlew dependencies
```

Режим `dependenicesInsight` позволяет сосредоточиться на конкретной зависимости
```bash
./gradlew dependenicesInsight \
    --configuration testRuntimeClasspath \
    --dependency junit-jupiter-api
```

