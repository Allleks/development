```cpp
error C4996: 'QVariant::Type': Use QMetaType::Type instead.

QMap<QString, QVariant::Type> baseTypes; // Устарело в Qt6
```
в Qt6 используем *QMetaType::Type*
```cpp
QMap<QString, QMetaType::Type> baseTypes;
```
___
если нужна поддержка с qt5
```cpp
// Используйте условную компиляцию
#if QT_VERSION < QT_VERSION_CHECK(6, 0, 0)
    QMap<QString, QVariant::Type> baseTypes;
#else
    QMap<QString, QMetaType::Type> baseTypes;
#endif
```
___
## 📊 Таблица соответствий типов

|**Старый тип (QVariant::Type)**|**Новый тип (QMetaType::Type)**|**Значение**|
|---|---|---|
|`QVariant::Invalid`|`QMetaType::UnknownType`|Невалидный тип|
|`QVariant::String`|`QMetaType::QString`|Строка QString|
|`QVariant::Int`|`QMetaType::Int`|Целое число|
|`QVariant::Double`|`QMetaType::Double`|Число с плавающей точкой|
|`QVariant::Bool`|`QMetaType::Bool`|Логический тип|
|`QVariant::List`|`QMetaType::QVariantList`|Список QVariant|
|`QVariant::Map`|`QMetaType::QVariantMap`|Словарь QVariantMap|
|`QVariant::UserType`|`QMetaType::User`|Пользовательский тип|
## ⚠️ Важные нюансы

1. **Метод `QVariant::type()` тоже устарел**
- Вместо `var.type()` используйте `static_cast<QMetaType::Type>(var.typeId())`
```cpp
void processData(const QVariant &data) {
    QMetaType::Type type = static_cast<QMetaType::Type>(data.typeId());
    
    if (type == QMetaType::QString) {
        QString str = data.toString();
        // обработка строки
    } else if (type == QMetaType::Int) {
        int value = data.toInt();
        // обработка числа
    }
}
```