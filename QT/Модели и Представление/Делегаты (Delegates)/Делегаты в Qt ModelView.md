**Делегат** = "Маленький контроллер" для **отрисовки и редактирования** отдельных ячеек/элементов.
___
### Архитектура:
```cpp
View ── запрашивает отрисовку ──→ Delegate
       ↓                          ↓
       отображает результат ←─── рисует/редактирует
```
### Задачи делегата:
1. **Отрисовка** данных (`paint()`)
2. **Редактирование** данных (`createEditor()`, `setEditorData()`)
3. **Сохранение** данных (`setModelData()`)
4. **Управление размером** (`sizeHint()`)
---
## 2. Как работает с [[Модель|Model]]/[[Представление|View]]
```cpp
// View использует делегат для каждой ячейки
view->setItemDelegate(new MyDelegate(view));

// Когда View нужно отрисовать ячейку:
// 1. View вызывает delegate->paint(painter, option, index)
// 2. Delegate запрашивает данные у Model: index.data(role)
// 3. Delegate рисует как хочет
```
### Жизненный цикл редактирования:
```cpp
// 1. Пользователь начинает редактирование
void QAbstractItemView::edit(const QModelIndex &index) {
    // Создаем редактор
    QWidget* editor = delegate->createEditor(parent, option, index);
    
    // Заполняем данными из модели
    delegate->setEditorData(editor, index);
    
    // Показываем редактор
    editor->show();
    editor->setFocus();
}

// 2. Пользователь закончил редактирование
// Delegate сохраняет данные в модель
delegate->setModelData(editor, model, index);

// 3. Уничтожаем редактор
delegate->destroyEditor(editor, index);
```
---
## 3. Виды делегатов
**QStyledItemDelegate** (рекомендуется)
```cpp
class CustomDelegate : public QStyledItemDelegate {
    Q_OBJECT
public:
    // Основные методы:
    void paint(QPainter* painter, 
               const QStyleOptionViewItem& option,
               const QModelIndex& index) const override;
               
    QSize sizeHint(const QStyleOptionViewItem& option,
                   const QModelIndex& index) const override;
                   
    QWidget* createEditor(QWidget* parent,
                         const QStyleOptionViewItem& option,
                         const QModelIndex& index) const override;
                         
    void setEditorData(QWidget* editor,
                      const QModelIndex& index) const override;
                      
    void setModelData(QWidget* editor,
                     QAbstractItemModel* model,
                     const QModelIndex& index) const override;
};
```
**QItemDelegate** (устаревший)
- Упрощенный, меньше интеграции со стилями
- Использовать только если нужна обратная совместимость
---


---

## 7. Делегат vs Модель

|Задача|Делегат|Модель|
|---|---|---|
|**Как отобразить**|✅ Да|❌ Нет|
|**Как редактировать**|✅ Да|❌ Нет|
|**Что отобразить**|❌ Нет|✅ Да|
|**Хранение данных**|❌ Нет|✅ Да|
|**Валидация**|✅ UI-валидация|✅ Бизнес-валидация|
|**Сортировка**|❌ Нет|✅ Да|

---

## 8. Производительность делегатов

### **Плохо:**

cpp

void paint(...) const override {
    // Каждый раз загружаем изображение
    QPixmap pixmap(":/images/icon.png"); // ← ЗАГРУЗКА В paint()
    painter->drawPixmap(option.rect, pixmap);
}

### **Хорошо:**

cpp

class CachedIconDelegate : public QStyledItemDelegate {
    mutable QCache<QString, QPixmap> m_iconCache; // ← КЕШ
    
    void paint(...) const override {
        QString iconPath = index.data(IconPathRole).toString();
        QPixmap* pixmap = m_iconCache[iconPath];
        
        if (!pixmap) {
            pixmap = new QPixmap(iconPath);
            m_iconCache.insert(iconPath, pixmap);
        }
        
        painter->drawPixmap(option.rect, *pixmap);
    }
};

---

## 9. Шаблон для продакшен-делегата

cpp

class ProductionDelegate : public QStyledItemDelegate {
    Q_OBJECT
    
public:
    explicit ProductionDelegate(QObject* parent = nullptr);
    
    // Отрисовка
    void paint(QPainter* painter,
               const QStyleOptionViewItem& option,
               const QModelIndex& index) const override;
    
    // Размер
    QSize sizeHint(const QStyleOptionViewItem& option,
                   const QModelIndex& index) const override;
    
    // Редактирование
    QWidget* createEditor(QWidget* parent,
                         const QStyleOptionViewItem& option,
                         const QModelIndex& index) const override;
    
    void setEditorData(QWidget* editor,
                      const QModelIndex& index) const override;
    
    void setModelData(QWidget* editor,
                     QAbstractItemModel* model,
                     const QModelIndex& index) const override;
    
    // Валидация
    bool editorEvent(QEvent* event,
                     QAbstractItemModel* model,
                     const QStyleOptionViewItem& option,
                     const QModelIndex& index) override;
    
    // Подсказки
    bool helpEvent(QHelpEvent* event,
                   QAbstractItemView* view,
                   const QStyleOptionViewItem& option,
                   const QModelIndex& index) override;
    
private:
    // Вспомогательные методы
    QColor calculateColor(const QModelIndex& index) const;
    QString formatValue(const QVariant& value) const;
    
    // Кеширование
    mutable QCache<QModelIndex, QPixmap> m_pixmapCache;
    mutable QCache<QString, QColor> m_colorCache;
};