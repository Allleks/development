Есть два объекта, и в цикле можно пройтись по ним сразу
```cpp
ui.expression_table_1->setRowCount(rowCount);
ui.expression_table_2->setRowCount(rowCount);

for (auto* table : {ui.expression_table_1, ui.expression_table_2}) {
...
}
```
Очень удобно проводить операции с двумя и более элементами, код чистый и читаемый
```cpp
void EditExpressionWindow::setupExpressionTables()
{
	ui.expression_table_1->setColumnCount(7);
	ui.expression_table_2->setColumnCount(7);

	QStringList headers = {"Код", "Название", "Описание", "Ед.изм", "Мин. знач", "Макс.знач", "Тест. знач"};

	for (auto* table : {ui.expression_table_1, ui.expression_table_2}) 
	{
		table->setHorizontalHeaderLabels(headers);
		table->horizontalHeader()->setSectionResizeMode(QHeaderView::Stretch);
	}
}
```
___
Нужо ли ставить фокус при создании qTableWidget? и что он дает
```cpp
	ui.machineriesTable->setFocus();
```
чтобы выделение были строка только одна 
```cpp
// В коде после setupUi():
ui->expression_table_1->setSelectionMode(QAbstractItemView::SingleSelection);
ui->expression_table_1->setSelectionBehavior(QAbstractItemView::SelectRows);  // ← ВАЖНО!

// Или в Qt Designer установите:
// selectionBehavior: SelectRows
// selectionMode: SingleSelection


///
selectionMode:
  ◉ NoSelection           - нельзя выделять
  ◉ SingleSelection       - только один элемент (ячейка/строка)
  ◉ MultiSelection        - несколько элементов (Ctrl+клик)
  ◉ ExtendedSelection     - расширенный (Ctrl, Shift, мышью)
  ◉ ContiguousSelection   - смежные элементы (Shift)
```
____
### **Решение проблемы "1 роль":**

Если в Qt Designer видите "1 role", значит палитра не полностью настроена. Нужно:
1. В Qt Designer выбрать `expression_table_1`
2. Найти свойство `palette` в Property Editor
3. Нажать `...` (кнопку редактирования)
4. Выбрать роль (например, `Base`)
5. Выбрать цвет
6. Нажать OK
**Или программно:**
```cpp
// Установить все основные роли
QPalette palette;
palette.setColor(QPalette::Base, QColor(255, 255, 255));      // Белый фон
palette.setColor(QPalette::Text, QColor(0, 0, 0));            // Черный текст
palette.setColor(QPalette::Window, QColor(240, 240, 240));    // Серый фон окна
palette.setColor(QPalette::Button, QColor(200, 200, 200));    // Серая кнопка
palette.setColor(QPalette::Highlight, QColor(100, 150, 200)); // Синее выделение

ui.expression_table_1->setPalette(palette);
ui.expression_table_2->setPalette(palette);
```

**Совет:** Лучше настраивать цвета программно в конструкторе окна, а не в Qt Designer - так проще поддерживать и изменять.