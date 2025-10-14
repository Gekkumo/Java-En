---
tags:
  - Программирование
  - Java
  - Java-OOP
  - review
sr-due: 2025-10-15
sr-interval: 1
sr-ease: 154
---
**SOLID** - это набор из пяти основных принципов объектно-ориентированного программирования ([[ООП]]), предназначенных для создания понятного, гибкого и поддерживаемого кода.

## Зачем нужно?

- **Проблема:** Сложность поддержки, модификации и расширения кода.
- **Преимущества:**
    - Снижение связности компонентов
    - Упрощение тестирования
    - Повышение переиспользуемости кода
    - Облегчение рефакторинга
- **Без SOLID:** Код становится "спагетти-кодом", где изменения в одной части ломают другие.

## Суть концепта

- **S** - Single Responsibility Principle (Принцип единственной ответственности)
- **O** - Open/Closed Principle (Принцип открытости/закрытости)
- **L** - Liskov Substitution Principle (Принцип подстановки Барбары Лисков)
- **I** - Interface Segregation Principle (Принцип разделения интерфейсов)
- **D** - Dependency Inversion Principle (Принцип инверсии зависимостей)

## Как реализуется?

### 1. SRP - Принцип единственной ответственности

``` Java
// ПЛОХО: Класс делает слишком много
class User {
    void saveToDatabase() { /* ... */ }
    void sendEmail() { /* ... */ }
    void validateData() { /* ... */ }
}

// ХОРОШО: Разделение ответственности
class User {
    // только данные и логика предметной области
}
class UserRepository {
    void save(User user) { /* ... */ } // работа с БД
}
class EmailService {
    void sendEmail(User user) { /* ... */ } // отправка email
}
class UserValidator {
    boolean validate(User user) { /* ... */ } // валидация
}
```

### 2. OCP - Принцип открытости/закрытости

``` Java
// Базовый класс, который не меняем
abstract class Shape {
    public abstract double area();
}

// Добавляем новые фигуры без изменения существующего кода
class Rectangle extends Shape {
    private double width, height;
    public double area() { return width * height; }
}

class Circle extends Shape {
    private double radius;
    public double area() { return Math.PI * radius * radius; }
}

// Новую фигуру добавляем НЕ изменяя AreaCalculator
class AreaCalculator {
    public double calculateTotalArea(List<Shape> shapes) {
        return shapes.stream().mapToDouble(Shape::area).sum();
    }
}
```

### 3. LSP - Принцип подстановки Барбары Лисков

``` Java
// Нарушение LSP
class Rectangle {
    protected int width, height;
    
    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
}

class Square extends Rectangle {
    // Нарушение: квадрат меняет ожидаемое поведение
    void setWidth(int w) { 
        super.setWidth(w); 
        super.setHeight(w); // Неожиданное побочное действие!
    }
}

// Соблюдение LSP
abstract class Shape {
    abstract double area();
}

class Rectangle extends Shape {
    private int width, height;
    double area() { return width * height; }
}

class Square extends Shape {
    private int side;
    double area() { return side * side; }
}
```

### 4. ISP - Принцип разделения интерфейсов

``` Java
// ПЛОХО: Один "толстый" интерфейс
interface Worker {
    void work();
    void eat();
    void sleep();
}

// Проблема: Robot должен реализовать ненужные методы
class Robot implements Worker {
    void work() { /* ... */ }
    void eat() { /* не поддерживается! */ }
    void sleep() { /* не поддерживается! */ }
}

// ХОРОШО: Разделенные интерфейсы
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class Human implements Workable, Eatable, Sleepable {
    // реализует все методы
}

class Robot implements Workable {
    // реализует только work()
}
```

### 5. DIP - Принцип инверсии зависимостей

``` Java
// ПЛОХО: Зависимость от конкретной реализации
class EmailService {
    void sendEmail() { /* ... */ }
}

class Notification {
    private EmailService emailService; // жесткая зависимость
    
    public Notification() {
        this.emailService = new EmailService();
    }
}

// ХОРОШО: Зависимость от абстракции
interface MessageService {
    void sendMessage();
}

class EmailService implements MessageService {
    public void sendMessage() { /* ... */ }
}

class SMSService implements MessageService {
    public void sendMessage() { /* ... */ }
}

class Notification {
    private MessageService service; // зависимость от интерфейса
    
    // Внедрение зависимости через конструктор
    public Notification(MessageService service) {
        this.service = service;
    }
}
```

## Связи

- **Включает в себя:**
    - 5 конкретных принципов проектирования
    - Практики написания чистого кода
- **Является частью:**
    - Объектно-ориентированного программирования ([[ООП]])
    - Архитектуры программного обеспечения
- **Связанные концепты:**
    - [[Наследование]] (используется в принципах OCP, LSP)
    - [[Абстракция]] (основа DIP)
    - [[Полиморфизм]] (необходим для соблюдения принципов)
    - [[Инкапсуляция]] (способ организации кода)