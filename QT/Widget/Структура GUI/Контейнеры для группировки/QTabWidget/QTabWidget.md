[QTabWidget Class | Qt Widgets | Qt 6.10.1](https://doc.qt.io/qt-6/qtabwidget.html)
**QTabWidget** = Окно с вкладками как в браузере.
___
```
┌─────────────────────────────────┐
│ [Вкладка 1] [Вкладка 2] [ + ]   │ ← Заголовки вкладок
├─────────────────────────────────┤
│                                 │
│      Содержимое вкладки 1       │ ← Контент (любой QWidget)
│                                 │
└─────────────────────────────────┘
```
## Базовое использование
```cpp
#include <QApplication>
#include <QTabWidget>
#include <QLabel>
#include <QPushButton>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    
    // 1. Создаем QTabWidget
    QTabWidget tabWidget;
    tabWidget.setWindowTitle("Мое приложение");
    
    // 2. Создаем содержимое для вкладок
    QLabel* label1 = new QLabel("Это первая вкладка");
    QLabel* label2 = new QLabel("Это вторая вкладка");
    
    // 3. Добавляем вкладки
    tabWidget.addTab(label1, "Вкладка 1");
    tabWidget.addTab(label2, "Вкладка 2");
    
    // 4. Показываем окно
    tabWidget.resize(400, 300);
    tabWidget.show();
    
    return app.exec();
}
```
## Основные методы (что умеет QTabWidget)
### Добавление вкладок:
```cpp
// Просто добавить
tabWidget.addTab(new QLabel("Текст"), "Название");

// С иконкой
tabWidget.addTab(new QLabel("Текст"), QIcon("icon.png"), "Название");

// В определенную позицию
tabWidget.insertTab(1, new QPushButton("Кнопка"), "Середина");
```
### Управление вкладками:
```cpp
// Переключиться на вкладку
tabWidget.setCurrentIndex(0); // первая вкладка
tabWidget.setCurrentWidget(labelWidget); // по виджету

// Удалить вкладку
tabWidget.removeTab(0); // удалить первую

// Получить информацию
int count = tabWidget.count(); // сколько вкладок
QString title = tabWidget.tabText(0); // заголовок первой
QWidget* widget = tabWidget.widget(0); // содержимое первой
```
### Настройка внешнего вида:
```cpp
// Положение вкладок
tabWidget.setTabPosition(QTabWidget::North);  // сверху (по умолчанию)
tabWidget.setTabPosition(QTabWidget::South);  // снизу
tabWidget.setTabPosition(QTabWidget::West);   // слева
tabWidget.setTabPosition(QTabWidget::East);   // справа

// Можно ли закрывать вкладки
tabWidget.setTabsClosable(true);

// Можно ли перетаскивать вкладки
tabWidget.setMovable(true);

// Показывать кнопки прокрутки если много вкладок
tabWidget.setUsesScrollButtons(true);
```
### Как работать с разными типами содержимого
Каждая вкладка может содержать **любой QWidget**:
```cpp
// 1. Форма с полями ввода
QWidget* createFormTab()
{
    QWidget* widget = new QWidget();
    QFormLayout* layout = new QFormLayout(widget);
    
    layout->addRow("Имя:", new QLineEdit());
    layout->addRow("Email:", new QLineEdit());
    layout->addRow(new QPushButton("Сохранить"));
    
    return widget;
}
```

```cpp
// 2. Таблица
QWidget* createTableTab()
{
    QTableView* table = new QTableView();
    // ... настройка модели и т.д.
    return table;
}
```

```cpp
// 3. График
QWidget* createChartTab()
{
    QWidget* widget = new QWidget();
    // ... создание графика
    return widget;
}

// Использование:
tabWidget.addTab(createFormTab(), "Форма");
tabWidget.addTab(createTableTab(), "Таблица");
tabWidget.addTab(createChartTab(), "График");
```
## Обработка событий (сигналы)
```cpp
// Подписываемся на события TabWidget
connect(tabWidget, &QTabWidget::currentChanged, 
        [](int index) {
            qDebug() << "Переключились на вкладку:" << index;
        });

connect(tabWidget, &QTabWidget::tabCloseRequested,
        [](int index) {
            qDebug() << "Закрываем вкладку:" << index;
        });

connect(tabWidget, &QTabWidget::tabBarDoubleClicked,
        [](int index) {
            qDebug() << "Двойной клик по вкладке:" << index;
            // Можно переименовать вкладку
        });
```
___
## Что важно запомнить
1. **QTabWidget ≠ данные** - это только контейнер
2. **Каждая вкладка = QWidget** - можно положить что угодно
3. **Управляйте памятью** - удаляйте виджеты при закрытии
4. **Используйте сигналы** для реактивности
5. **Делайте вкладки независимыми** - каждая сама за себя