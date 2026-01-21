```cpp
#include <QTabWidget>
#include <QTableView>
#include <QStandardItemModel>
#include <QVBoxLayout>

// Создаем QTabWidget
QTabWidget *tabWidget = new QTabWidget(this);

// Создаем таблицу и модель
QTableView *tableView = new QTableView();
QStandardItemModel *model = new QStandardItemModel(3, 3, tableView);

// Заполняем модель
for (int row = 0; row < 3; ++row) {
    for (int col = 0; col < 3; ++col) {
        QStandardItem *item = new QStandardItem(
            QString("Строка %1, Колонка %2").arg(row).arg(col)
        );
        model->setItem(row, col, item);
    }
}

// Устанавливаем заголовки
model->setHorizontalHeaderLabels({"Колонка 1", "Колонка 2", "Колонка 3"});

// Применяем модель к таблице
tableView->setModel(model);

// Создаем контейнер для таблицы (необязательно, но полезно)
QWidget *tableContainer = new QWidget();
QVBoxLayout *layout = new QVBoxLayout(tableContainer);
layout->addWidget(tableView);
tableContainer->setLayout(layout);

// Добавляем таблицу во вкладку
tabWidget->addTab(tableContainer, "Таблица данных");

// Можно добавить еще вкладок
tabWidget->addTab(new QLabel("Другая информация"), "Инфо");
```
## Важные моменты:
1. **Управление памятью**: При удалении вкладки через `removeTab()` виджет не удаляется автоматически.
2. **Стилизация**: Вкладки можно кастомизировать через `setTabPosition()`, `setTabShape()`.
3. **Динамическое добавление**: Можно добавлять/удалять вкладки в runtime.