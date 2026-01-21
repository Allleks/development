Возвращает данные для **конкретной ячейки** (индекса) в модели для **определенной роли** (role). Это основной метод для получения данных из модели в представление (View).
___
### **Сигнатура:**
```cpp
QVariant data(const QModelIndex &index, int role = Qt::DisplayRole) const
```
### **Параметры:**
1. **`const QModelIndex &index`** - индекс запрашиваемой ячейки
    - Содержит информацию о строке, столбце и родительском элементе
    - Если `index.isValid()` возвращает `false` - запрашивается несуществующий элемент
2. **`int role = Qt::DisplayRole`** - **роль** данных
    - Определяет, **какие именно** данные нужны для этой ячейки
    - По умолчанию - `DisplayRole` (то, что показывается в ячейке)
### **Возвращаемое значение:**
- **`QVariant`** - универсальный контейнер данных Qt
- Если данных для данной роли нет, возвращаем **`QVariant()`** (невалидный QVariant)
- Если индекс невалиден, также возвращаем `QVariant()`
---
## **Ключевые моменты:**

### **1. Роли (Roles) - ЭТО ВАЖНО!**

### [[Roles| Про роли и их типы]]
### **2. Реализация по шагам:**
```cpp
QVariant MyModel::data(const QModelIndex &index, int role) const
{
    // Шаг 1: Проверяем валидность индекса
    if (!index.isValid())
        return QVariant();
    
    // Шаг 2: Проверяем границы (опционально, но рекомендуется)
    if (index.row() >= rowCount() || index.column() >= columnCount())
        return QVariant();
    
    // Шаг 3: Получаем данные из внутренней структуры
    // Например, из вектора или дерева
    MyDataItem* item = getItemByIndex(index);
    if (!item)
        return QVariant();
    
    // Шаг 4: Обрабатываем разные роли
    switch (role) {
    case Qt::DisplayRole:
    case Qt::EditRole:
        return item->text();  // Основной текст
        
    case Qt::DecorationRole:
        return item->icon();  // Иконка
        
    case Qt::ToolTipRole:
        return item->tooltip();  // Подсказка
        
    case Qt::ForegroundRole:
        if (item->isImportant())
            return QBrush(Qt::red);  // Красный текст для важных
        
    case Qt::BackgroundRole:
        if (index.row() % 2 == 0)
            return QBrush(QColor(245, 245, 245));  // Зебра
        
    case Qt::TextAlignmentRole:
        if (index.column() == 1)  // Для второго столбца
            return Qt::AlignCenter;
            
    case Qt::CheckStateRole:
        if (index.column() == 0)  // Для первого столбца
            return item->isChecked() ? Qt::Checked : Qt::Unchecked;
    }
    
    // Шаг 5: Для всех остальных ролей возвращаем невалидный QVariant
    return QVariant();
}
```
### **3. Пример из реальной жизни (модель списка файлов):**
```cpp
QVariant FileSystemModel::data(const QModelIndex &index, int role) const
{
    if (!index.isValid())
        return QVariant();
    
    FileInfo* fileInfo = m_files[index.row()];
    
    switch (role) {
    case Qt::DisplayRole:
        switch (index.column()) {
        case 0: return fileInfo->name();      // Имя файла
        case 1: return formatSize(fileInfo->size());  // Размер
        case 2: return fileInfo->modifiedDate(); // Дата изменения
        case 3: return fileInfo->type();      // Тип
        }
        break;
        
    case Qt::DecorationRole:
        if (index.column() == 0)
            return fileInfo->isDirectory() ? m_dirIcon : m_fileIcon;
        break;
        
    case Qt::ToolTipRole:
        return QString("Путь: %1\nРазмер: %2")
               .arg(fileInfo->path())
               .arg(formatSize(fileInfo->size()));
               
    case Qt::ForegroundRole:
        if (fileInfo->isHidden())
            return QBrush(Qt::gray);  // Скрытые файлы серым
        
    case Qt::TextAlignmentRole:
        if (index.column() == 1)  // Выравнивание размера по правому краю
            return Qt::AlignRight | Qt::AlignVCenter;
    }
    
    return QVariant();
}
```
### **4. Важные нюансы:**
1. **Эффективность**: `data()` вызывается очень часто, должен быть быстрым.
2. **Кеширование**: Не кешируйте QVariant'ы между вызовами - Qt делает это сам.
3. **Инвалидация**: При изменении данных вызывайте `dataChanged()`, чтобы представление обновилось.
4. **Роли по умолчанию**: Всегда обрабатывайте хотя бы `DisplayRole`.
5. **Столбцы**: Проверяйте `index.column()` для разных столбцов таблицы.
### **5. Когда вызывается `data()`:**
- При отрисовке ячейки в представлении
- При обновлении представления (`dataChanged()`)
- При изменении размера, сортировке, фильтрации
- При запросе всплывающих подсказок
- При драг-н-дроп операциях (если настроено)
___
**Главная идея**: `data()` - это **"переводчик"** между внутренним хранением данных и тем, как они должны отображаться/использоваться в UI. Одна ячейка может возвращать разные данные в зависимости от роли!