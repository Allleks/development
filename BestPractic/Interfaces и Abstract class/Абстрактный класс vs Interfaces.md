**virtual методы** - **Правило:**
- Чисто виртуальные методы (`= 0`) → **обязательны** к реализации
- Обычные виртуальные методы → можно переопределить, но не обязательно
- Не виртуальные методы → нельзя переопределить
### **Чистый Интерфейс (Pure Interface)**
```cpp
// ТОЛЬКО чисто виртуальные методы (=0) и виртуальный деструктор
class IPureInterface {
public:
    virtual void doWork() = 0;          // ДОЛЖЕН быть реализован
    virtual ~IPureInterface() = default; // обязательно виртуальный
    // НЕТ полей данных
    // НЕТ методов с реализацией
};

// Интерфейс - класс только с чистыми виртуальными методами (= 0)
class ILogger {
public:
    virtual void log(const std::string& message) = 0;
    virtual ~ILogger() = default;
};

// Реализация интерфейса
class FileLogger : public ILogger {
public:
    void log(const std::string& message) override {
        // запись в файл
    }
};

class ConsoleLogger : public ILogger {
public:
    void log(const std::string& message) override {
        // вывод в консоль
    }
};

// Использование
void process(ILogger* logger) {
    // Можем передать любой наследник
    logger->log("Сообщение");
}

int main() {
    FileLogger fileLogger;
    ConsoleLogger consoleLogger;
    
    //Интерфейсы всегда передаются по ссылке, указателю или умному указателю. Копия недопустима!(Будет срез данных)
    process(&fileLogger);    // Работает
    process(&consoleLogger); // Работает
}
```
**Чеклист: Что использовать?**
1. **`void func(const Interface&)`** - когда объект существует гарантированно и мы его не меняем
2. **`void func(Interface&)`** - когда нужно изменить объект
3. **`void func(Interface*)`** - когда объект опционален (может быть nullptr)
4. **`void func(std::unique_ptr<Interface>)`** - передача владения
5. **`void func(std::shared_ptr<Interface>)`** - разделяемое владение
6. **❌ `void func(Interface)`** - НИКОГДА для полиморфных объектов!
___
### **Абстрактный класс (Abstract Base Class - ABC)**
```cpp
// Может иметь реализацию, но хотя бы один чисто виртуальный метод
class AbstractClass {
protected:
    int commonData;  // МОЖЕТ иметь поля
    
public:
    virtual void mustImplement() = 0;  // чисто виртуальный
    
    virtual void canOverride() {       // виртуальный с реализацией
        // базовая реализация
    }
    
    void concreteMethod() {            // обычный метод
        // всегда эта реализация
    }
    
    virtual ~AbstractClass() = default;
};
```
---
### **Используй ИНТЕРФЕЙС, когда:**
```cpp
// 1. Нужна полная абстракция
class IDatabase {
public:
    virtual void connect() = 0;
    virtual void disconnect() = 0;
    virtual ~IDatabase() = default;
    // Никакой реализации - только контракт
};

// 2. Несколько наследований (в C++ можно)
class MyClass : public IDatabase, public ILogger, public ISerializable {
    // Реализуем все интерфейсы
};
```

### **Используй АБСТРАКТНЫЙ КЛАСС, когда:**
```cpp
// 1. Есть общая логика для наследников
class Shape {
protected:
    double x, y;  // общее состояние
    
public:
    Shape(double x, double y) : x(x), y(y) {}
    
    virtual double area() const = 0;  // каждый должен реализовать
    
    // Общая реализация для всех фигур
    void move(double dx, double dy) {
        x += dx;
        y += dy;
    }
};
```
---
### **❌ Нельзя создать экземпляр ни интерфейса, ни абстрактного класса!**
```cpp
class IAnimal {
public:
    virtual void speak() = 0;
    virtual ~IAnimal() = default;
};

class Dog : public IAnimal {
public:
    void speak() override { cout << "Гав!"; }
};

int main() {
    // IAnimal animal;      // ❌ ОШИБКА: cannot declare variable of abstract type
    // Animal* a = new IAnimal(); // ❌ ОШИБКА
    
    Dog dog;                // ✅ OK - конкретный класс
    IAnimal* animal = new Dog(); // ✅ OK - указатель на интерфейс
    
    animal->speak();        // ✅ Вызывает Dog::speak()
}
```
**Почему нельзя:**
- Чисто виртуальные методы не имеют реализации
- Компилятор не знает, что делать при вызове такого метода
- Абстрактные классы - это "неполные" классы