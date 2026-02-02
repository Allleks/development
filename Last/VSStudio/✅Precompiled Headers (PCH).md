Пред компилируемые заголовочные файлы
## 1. СОЗДАНИЕ PCH ФАЙЛОВ

### Структура файлов:

text

project/
├── stdafx.h          ← ЗАГОЛОВОЧНЫЙ файл PCH
├── stdafx.cpp        ← ИСТОЧНИК для генерации PCH
├── main.cpp          ← Обычный .cpp файл
└── OtherFile.cpp     ← Другой .cpp файл

### Содержимое файлов:

**stdafx.h** (заголовок PCH):

cpp

#pragma once

// ВСЕ общие заголовки, которые используются в большинстве .cpp файлов
#include <QtCore>
#include <QtGui>
#include <QtWidgets>
#include <QApplication>
// ... другие часто используемые заголовки

**stdafx.cpp** (только для генерации PCH):

cpp

// В ЭТОМ ФАЙЛЕ ТОЛЬКО ОДНА СТРОЧКА!
#include "stdafx.h"
// Больше ничего! Никакого кода!

## 2. НАСТРОЙКА В VISUAL STUDIO

### Для stdafx.cpp:

text

Precompiled Header: Create (/Yc)
Precompiled Header File: stdafx.h

### Для ВСЕХ остальных .cpp файлов:

text

Precompiled Header: Use (/Yu)
Precompiled Header File: stdafx.h

## 3. ПРАВИЛО ДЛЯ КАЖДОГО .CPP ФАЙЛА

**Да, В КАЖДОМ .cpp файле ПЕРВОЙ СТРОЧКОЙ должен быть:**

cpp

#include "stdafx.h"
// Затем уже остальные include

### Пример правильного файла:

cpp

// main.cpp - ПРАВИЛЬНО
#include "stdafx.h"           // ← ПЕРВАЯ И ЕДИНСТВЕННАЯ строка в начале!
#include "MainWindow.h"       // ← Уже после stdafx.h
#include <QMessageBox>        // ← Если не включен в stdafx.h

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    MainWindow window;
    window.show();
    return app.exec();
}

## 4. ЧТО НЕЛЬЗЯ ДЕЛАТЬ

### ❌ НЕПРАВИЛЬНО:

cpp

// Пустая строка перед
                      // ← ПУСТАЯ СТРОКА!
#include "stdafx.h"   // ← ОШИБКА! Не первая строка

// Или комментарий перед
/* Комментарий */     // ← КОММЕНТАРИЙ!
#include "stdafx.h"   // ← ОШИБКА! Не первая строка

// Или другой include перед
#include <iostream>   // ← ДРУГОЙ INCLUDE!
#include "stdafx.h"   // ← ОШИБКА! Не первая строка

## 5. ПРОЦЕСС КОМПИЛЯЦИИ

1. **ПЕРВЫМ компилируется stdafx.cpp** → создается `TechMap.pch`
    
2. **Затем компилируются остальные .cpp** → используют готовый `TechMap.pch`
    
3. **Каждый .cpp начинается с `#include "stdafx.h"`** → компилятор заменяет на прекомпилированный код
    

## 6. ДЛЯ ВАШЕГО ПРОЕКТА ТЕХМАР

### Проверьте:

**1. stdafx.cpp настройки:**

- Откройте свойства stdafx.cpp
    
- C/C++ → Precompiled Headers
    
- Должно быть: `Create (/Yc)` и `stdafx.h`
    

**2. TechMap.cpp:**

cpp

// TechMap.cpp - ДОЛЖЕН ВЫГЛЯДЕТЬ ТАК:
#include "stdafx.h"      // ← САМАЯ ПЕРВАЯ СТРОКА ФАЙЛА!

#include "TechMap.h"
#include "ui_TechMap.h"

TechMap::TechMap(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::TechMap)
{
    ui->setupUi(this);
}

// ... остальной код

**3. main.cpp:**

cpp

// main.cpp - ДОЛЖЕН ВЫГЛЯДЕТЬ ТАК:
#include "stdafx.h"      // ← САМАЯ ПЕРВАЯ СТРОКА ФАЙЛА!

#include "TechMap.h"

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    TechMap window;
    window.show();
    return app.exec();
}

## 7. ЕСЛИ ФАЙЛ НЕ ИСПОЛЬЗУЕТ PCH

Если какой-то .cpp файл НЕ должен использовать PCH:

1. В свойствах файла: `Not Using Precompiled Headers`
    
2. Убрать `#include "stdafx.h"`
    
3. Добавить нужные заголовки вручную
    

## 8. КОНТРОЛЬНЫЙ СПИСОК

- `stdafx.h` содержит общие заголовки
    
- `stdafx.cpp` содержит только `#include "stdafx.h"`
    
- `stdafx.cpp` имеет настройку `Create (/Yc)`
    
- Все .cpp файлы имеют настройку `Use (/Yu)` (кроме stdafx.cpp)
    
- Каждый .cpp начинается с `#include "stdafx.h"` (первая строка!)
    
- Никаких пустых строк/комментариев перед `#include "stdafx.h"`