### Models
* Qt models tree и прочее как выделять ячейку(QModelIndex и производить операции с ней)
```cpp
QPair<QModelIndex, AbstractOperationsTreeViewItem*> OperationsWindow::getCurrentOperationsTreeItem(const QItemSelection& selectedItems) const
{
	try
	{
		auto selectionModel = operationsTreeView.selectionModel();
		if (selectionModel == nullptr)
{
	qCritical() << "SELECTIONMODEL IS NULL!";

	return QPair<QModelIndex, AbstractOperationsTreeViewItem*>();
	return QPair<QModelIndex, AbstractOperationsTreeViewItem*>(
	selectedIndexes.first(), 
	static_cast<AbstractOperationsTreeViewItem*>(selectedIndexes.first().internalPointer()));
}
```
Что означает Qt::UserRole? и как его использовать
```cpp
auto currentComponentId = componentVariations->at(item->data(Qt::UserRole).toInt())->GetComponentType()->GetId();
```
___
### Работа с событиями в qt 
```cpp
void closeEvent(QCloseEvent* event) override;

event->ignore();
event->accept();

emit onClose(true/false);
```
___
### QObjec когда используется? такая запись
```cpp
QObject::connect(
```
___
### Совет от бывалых про сигналы и слоты
Соединение всех слотов и сигналов надо выполнять в **главном окне,** так как именно оно знает всё о формах, которые в нём используются.
___
# Реализовать сериализацию/десериализацию данных ????
___
**QStrintList** - 
```cpp
QStringList headers = {"Код", "Название", "Описание", "Ед.изм", "Мин. знач", "Макс.знач", "Тест. знач"};
```
___
### **Слоты без параметров** - напрямую
```cpp
// Если метод объявлен как слот и не принимает параметров
connect(button, &QPushButton::clicked, this, &MyClass::someSlot);
```
### **Методы с параметрами или обычные функции** - через лямбду
```cpp
// Best practice для вашего случая
connect(ui.platformHeightExpressionEdit_button, &QPushButton::clicked,
        this, [this]() {
    // Лямбда захватывает this и может обращаться к UI
    QString text = ui.platformHeightExpression_label->text();
    showEditExpressionWindow(text);  // Вызов метода с параметром
});
```
**Вывод:** Для вызова методов с параметрами или любой сложной логики обработки используйте лямбды - это современный и рекомендуемый подход в Qt.
___
## sourcegit 
не смог сделать слияние в старую  ветку(ошибка), решил вопрос через bash консоль git так все ок
___
