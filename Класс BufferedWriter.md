### Запись файла 

В Java 7 был добавлен пакет `java.nio.file` и конструкция try-with-resources
```java
import java.io.BufferedWriter;
import java.nio.file.*;
import java.io.IOException;

public void writeLineToFile(Path pathToFile, String line) throws IOException {
  // это что-то вроде менеджера контекста with в Python
  try (BufferedWriter writer = Files.newBufferedWriter(path)) {
    writer.write(String.format("%s\n", line));
  }

  if (Files.exists(pathToFile)) // проверка существования
    System.out.println(
      String.format(
        "File '%s' successfully written!",
        pathToFile.getFileName()
      )
    );
}

// вызов
writeLineToFile(Paths.get("./test.txt"), "java");
```

На Python решение выглядело бы так
```python
from pathlib2 import Path

def write_line_to_file(path_to_file: Path, line: str) -> None:
    with open(path_to_file, mode="w", encoding="utf-8") as f:
        f.write(line)

    if path_to_file.exists():
        print(f"File {path_to_file.name!r} successfully written!")

# вызов
write_line_to_file(Path("./test.txt"), line="python")
```
