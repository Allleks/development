### В одном предложении:
**Forward declaration** — "я знаю, что такой класс есть, но не знаю деталей".  
**PIMPL** — "вся реализация полностью скрыта, даже от компилятора".
___
## Forward Declaration (Предварительное объявление)

### Что это:
Объявление класса без его полного определения.
### Зачем:
Избежать инклюда заголовка, уменьшить зависимости.
### Пример:
```cpp

// Вместо: #include "DataObject.h"
class DataObject;  // ← Forward declaration

class MyClass {
    DataObject* m_ptr;  // Работает с указателем
    DataObject& m_ref;  // Или ссылкой
    // DataObject m_obj;  // ERROR! Нужен полный тип
};
```
**Использовать когда:** нужен только указатель/ссылка на класс.

## PIMPL (Private Implementation / Pointer to Implementation)

### Что это:
Сокрытие реализации класса через указатель на приватную структуру.
### Зачем:
1. Сокращение времени компиляции
2. Полная инкапсуляция
3. Стабильный бинарный интерфейс (ABI)
### Пример:
```cpp
// Класс.h - публичный интерфейс
#pragma once
#include <memory>

class MyClass {
public:
    MyClass();
    ~MyClass();
    void doSomething();
    
private:
    struct Impl;  // ← Только объявление
    std::unique_ptr<Impl> pImpl;  // ← Указатель на реализацию
};

// Класс.cpp - реализация
#include "Класс.h"
#include <vector>
#include <string>

struct MyClass::Impl {  // ← Полное определение
    std::vector<int> data;
    std::string name;
    
    void privateMethod() { /* ... */ }
};

MyClass::MyClass() : pImpl(std::make_unique<Impl>()) {}
MyClass::~MyClass() = default;  // Нужен для unique_ptr<Impl>

void MyClass::doSomething() {
    pImpl->privateMethod();  // ← Работа через указатель
}
```
**Ключевые моменты PIMPL:**
- Все приватные члены в структуре `Impl`
- Деструктор должен быть в .cpp (из-за `unique_ptr`)
- Изменения в `Impl` не требуют перекомпиляции пользователей класса
---
## Когда что использовать:

|Ситуация|Forward Declaration|PIMPL|
|---|---|---|
|Только указатель на класс|✅|⚠️ (избыточно)|
|Полная инкапсуляция|❌|✅|
|Частые изменения приватных членов|❌|✅|
|Библиотека для сторонних разработчиков|❌|✅|
|Простая структура класса|✅|❌|
___
