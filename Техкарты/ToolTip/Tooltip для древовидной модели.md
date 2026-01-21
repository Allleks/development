| [Как при наведении на ячейку QTableWidget вывести подсказку с содержимым ячейки? - C++ Qt - Киберфорум](https://www.cyberforum.ru/qt/thread1566838.html?ysclid=mjqyvsso5g235367183)                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                                                                                                                                                                                                                                                                     |
| QTableWidgetItem, которыми населяется QTableWidget имеют метод setData(int role, const QVariant & value). Так вот этот самый role указывает на роль данных хранимых в ячейке. И среди ролей есть такая Qt::ToolTipRole - которая и отвечает за одноименный ToolTip. |
|                                                                                                                                                                                                                                                                     |
| [QTableWidgetItem Class \| Qt Widgets 5.15.19](https://doc.qt.io/archives/qt-5.15/qtablewidgetitem.html#setToolTip)                                                                                                                                                 |
___
## Способ 3: Простое включение tooltip для усеченного текста

Если вам нужно показывать tooltip только когда текст не помещается:
```cpp
// В классе вашей модели (унаследованном от QAbstractItemModel)
QVariant YourModel::data(const QModelIndex &index, int role) const
{
    if (role == Qt::ToolTipRole)
    {
        // Показывать tooltip только если есть текст в DisplayRole
        QString displayText = data(index, Qt::DisplayRole).toString();
        if (!displayText.isEmpty())
        {
            return displayText;
        }
    }
    
    // ... остальная логика
}
```
## Включение tooltip в QTreeView
Не забудьте включить поддержку tooltip в вашем QTreeView:
```cpp
treeView->setMouseTracking(true); // Для отслеживания движения мыши
treeView->setToolTipDuration(5000); // Длительность показа tooltip в миллисекундах (опционально)
```
## Важные моменты:
1. Убедитесь, что сигнатура вашего метода `data()` соответствует интерфейсу QAbstractItemModel
2. Если вы используете кастомную модель, проверьте, что она правильно возвращает данные для всех ролей
3. Для многострочных tooltip используйте `\n` для разделения строк
4. Для локализации используйте `tr()` как в примерах выше
___
Решение такое, находим метод data и в нем возвращаем case Qt::ToolTipRole: возвращаем что нужно
```cpp
QVariant AbstractTreeViewModel::data(const QModelIndex& index, int role) const
{
	try
	{
		if (!index.isValid())
		{
			return { };
		}

		const auto* item = static_cast<const AbstractTreeViewItem*>(index.constInternalPointer());

		switch (role)
		{
		case Qt::CheckStateRole:
			if (index.column() == 0)
			{
				return static_cast<int>(item->getCheckState());
			}

			return { };

		case Qt::UserRole:
			return item->getId();

		case Qt::FontRole:
			return item->getFont();

		case Qt::BackgroundRole:
			return item->getBackgroundColor();

		case Qt::DisplayRole:
			return item->data(index.column());

		case Qt::SizeHintRole:
			return index.column() == 0 ? QSize(200, 22) : QVariant();	// Принудительно расширен первый столбец и увеличена высота строки таблицы

		case Qt::DecorationRole:
			return index.column() == 0 ? QIcon(":/file/file_icons/file-pencil.svg").pixmap(QSize(24, 24)) : QVariant();

		case Qt::ToolTipRole:
			return item->data(index.column());
		default:
			return { };
		}
	}
	catch (const std::exception& ex)
	{
		qCritical() << ex.what();
	}

	return { };
}
```