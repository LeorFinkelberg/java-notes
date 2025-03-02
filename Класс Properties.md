Экземпляр класса `Properties` содержит пару ключ-значение в виде строки. Даже если информация в файле свойств будет изменена, перекомпиляция не потребуется!

Пример
```java
// сохранение свойств -> XML

public void saveProperties(String location) throws IOException {
  Properties prop = new Properties();
  prop.setProperties("name", "Steve");
  prop.setProperties("color", "green");

  File file = new File(location);
  if (!file.exists()) {
    file.createNewFile();
  }

  prop.storeToXML(new FileOutputStream(file), "testing properties with XML");
}
```

Пример
```java
// чтение свойств

public static void loadProperties(String location)
    throws FileNotFoundException, IOException {
  Properties prop = new Properties();

  File file = new File(location);
  if (file.exists()) {
    prop.loadFromXML(new FileInputStream(file));

    for (String name : prop.stringPropertyNames()) {
	  System.out.println(name + "=" + prop.getProperty(name));
    }
  } else {
    System.out.println("Error: No file found at: " + location);  // Ошибка: такой файл не найден в
  }
}
```