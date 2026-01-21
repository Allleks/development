```cpp
QModelIndex TreeModel::index(int row, int column, const QModelIndex &parent) const {
    // parent - это индекс РОДИТЕЛЯ
    // row - позиция среди детей этого родителя
    
    if (!hasIndex(row, column, parent)) return QModelIndex();
    
    TreeItem* parentItem;
    if (!parent.isValid()) {
        parentItem = rootItem; // Запрашиваем детей корня
    } else {
        parentItem = static_cast<TreeItem*>(parent.internalPointer());
    }
    
    TreeItem* childItem = parentItem->child(row); // Берем ребенка по номеру
    if (childItem) {
        return createIndex(row, column, childItem);
    }
    
    return QModelIndex();
}
```

```cpp
(Корень) - HeaderTreeViewItem
├── [Блок: "ТО"] - OperationsBlockTreeViewItem
│   ├── [Группа: "Ежедневное"] - GroupOperationsTreeViewItem
│   │   └── [Операция: "Проверка"] - OperationOperationsTreeViewItem
│   └── [Группа: "Ежемесячное"] - GroupOperationsTreeViewItem
└── [Блок: "Ремонт"] - OperationsBlockTreeViewItem


// Получить индекс Блока "ТО"
// row=0 (первый ребенок корня), parent=корень
QModelIndex index_блока_ТО = model->index(0, 0, QModelIndex());

// Получить индекс Группы "Ежедневное"
// row=0 (первый ребенка блока ТО), parent=блок_ТО
QModelIndex index_группы_ежедневное = model->index(0, 0, index_блока_ТО);
```
## **Но есть нюанс с получением индекса:**
```cpp
// 1. Сначала получаем индекс Блока А
QModelIndex index_блока_А = model->index(0, 0, QModelIndex());
// row=0 (первый ребенок корня), parent=корень

// 2. Теперь получаем индекс Группы А2
QModelIndex index_группы_А2 = model->index(1, 0, index_блока_А);
// row=1 (второй ребенок Блока А), parent=индекс_блока_А
```