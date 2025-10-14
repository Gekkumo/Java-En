---
  - Программирование
  - Java
  - review
---
StringBuffer - это потокобезопасный (thread-safe) аналог [[StringBuilder]], появившийся еще в Java 1.0. Он используется для эффективной конкатенации строк в многопоточной среде. Внутренне он очень похож на StringBuilder, но с ключевым отличием - **синхронизация методов**.
## Внутреннее представление

Как и `StringBuilder`, `StringBuffer` наследуется от `AbstractStringBuilder` и хранит символы в **массиве (`char[]` или `byte[]`)**.
### Поля класса (упрощённо)**:

public final class StringBuffer extends AbstractStringBuilder {
    char[] value;  // массив символов (в Java 9+ может быть byte[])
    int count;     // текущее количество символов
}

(Фактически, `StringBuffer` и `StringBuilder` используют общую логику из `AbstractStringBuilder`, но `StringBuffer` добавляет синхронизацию.)
## Синхронизация методов

Главное отличие от `StringBuilder` — **все ключевые методы `StringBuffer` синхронизированы** (`synchronized`):

@Override
public synchronized StringBuffer append(String str) {
    super.append(str);
    return this;
}

@Override
public synchronized String toString() {
    return new String(value, 0, count);
}

// Аналогично для insert(), delete(), reverse() и др.

Это делает `StringBuffer` **потокобезопасным**, но **медленнее**, чем `StringBuilder`.
## Динамическое расширение буфера

Как и в `StringBuilder`, при добавлении символов массив увеличивается по формуле:

newCapacity = (oldCapacity * 2) + 2

Но из-за синхронизации операции занимают больше времени.
## **4. Пример работы в многопоточной среде**

StringBuffer buffer = new StringBuffer();

Runnable task = () -> {
    for (int i = 0; i < 1000; i++) {
        buffer.append("x");  // потокобезопасно
    }
};

Thread t1 = new Thread(task);
Thread t2 = new Thread(task);
t1.start();
t2.start();
t1.join();
t2.join();

System.out.println(buffer.length());  // Гарантированно 2000

Без `synchronized` (как в `StringBuilder`) могло бы возникнуть **состояние гонки (race condition)**.
## Разница между `StringBuffer` и `StringBuilder`

|**Критерий**|**StringBuffer**|**StringBuilder**|
|---|---|---|
|**Потокобезопасность**|Да (синхронизирован)|Нет|
|**Производительность**|Медленнее (из-за `synchronized`)|Быстрее|
|**С версии Java**|1.0|1.5|
|**Рекомендуется**|Только при работе с несколькими потоками|В однопоточных сценариях|

## Когда использовать `StringBuffer`?

- **Многопоточные приложения**, где несколько потоков изменяют одну строку.
    
- **Устаревший код** (до Java 5, где не было `StringBuilder`).

В остальных случаях **лучше `StringBuilder`** — он быстрее.
## Вывод:

- `StringBuffer` — это **потокобезопасный `StringBuilder`**.
    
- Внутри использует **`char[]` / `byte[]`** и динамически расширяется.
    
- Все ключевые методы (`append`, `insert`, `toString`) **синхронизированы**.
    
- **Медленнее `StringBuilder`**, но безопасен в многопоточных сценариях.
    
- В Java 9+ также оптимизирован через `byte[]` для Latin-1.

**Оптимальное использование:**

StringBuffer buffer = new StringBuffer();  // если нужна потокобезопасность
buffer.append("Hello").append(" ").append("World");  // method chaining
String result = buffer.toString();

Если многопоточность не нужна — **используйте `StringBuilder`**.

#Программирование #Java 