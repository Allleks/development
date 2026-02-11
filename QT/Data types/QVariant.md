[QVariant Class | Qt Core | Qt 6.10.1](https://doc.qt.io/qt-6/qvariant.html)
___
универсальный контейнер для хранения значений разных типов. (похож на std::variant std::any), но со встроенной серилизацией и интеграцией с мета-системой Qt
___
QList проверить в QMetaType напрямую нельзя, только через qMetaTypeId
```cpp
	case QVariant::Bool:
		value = ((QCheckBox*) sender)->isChecked();
		break;
	case QMetaType::Double:
		value = ((QDoubleSpinBox*) sender)->value();
		break;
		case qMetaTypeId<QList<QString>>():
		value.clear();
		value = ((QComboBox*) sender)->currentText();
		break;
	default:
		break;
	}
qMetaTypeId<QList<QString>>()
```

qMetaTypeId вопрос что за волшебный метод
___
#qt5 #qt6 #миграция
В Qt6 нужно заменить устаревшие конструкторы `QVariant(QVariant::Type)` на корректные. Вот полный исправленный код:
## 🔄 Таблица замен для Qt6

| Было в Qt5                     | Стало в Qt6                                             | Примечание                 |
| ------------------------------ | ------------------------------------------------------- | -------------------------- |
| `QVariant(QVariant::Int)`      | `QVariant(0)` или `QVariant::fromValue<int>(0)`         | `int` со значением 0       |
| `QVariant(QVariant::Double)`   | `QVariant(0.0)` или `QVariant::fromValue<double>(0.0)`  | `double` со значением 0.0  |
| `QVariant(QVariant::LongLong)` | `QVariant(0LL)` или `QVariant::fromValue<qlonglong>(0)` | `long long` со значением 0 |
| `QVariant(QVariant::Bool)`     | `QVariant(false)`                                       | Явное создание `bool`      |
| `QVariant(QVariant::QString)`  | `QString()` или `""`                                    | Оба варианта работают      |
#### **В Qt 5** использовался `QVariant::Type` -> **В Qt 6** используется `QMetaType::Type`
 
 Способ 1: Явное преобразование типа
```cpp
_filter->baseTypes.insert(key, static_cast<QMetaType::Type>(item->baseParams[key].typeId()));
```
Способ 2: Использование метатипа ❤️‍🔥Qt 6
```cpp
_filter->baseTypes.insert(key, item->baseParams[key].metaType().id());
```
Способ 3: Получение типа через QVariant
```cpp
QVariant variant = item->baseParams[key];
_filter->baseTypes.insert(key, variant.metaType().id());
```
Способ 4: Использование typeId() (если уже есть QMetaType::Type)
```cpp
// Проверьте тип возвращаемого значения
QMetaType metaType = item->baseParams[key].metaType();
_filter->baseTypes.insert(key, metaType.id());
```
Способ 5: Если item->baseParams[key] - это QVariant
```cpp
auto variant = item->baseParams[key];
_filter->baseTypes.insert(key, static_cast<QMetaType::Type>(variant.userType()));
```
___
Основные изменения для Qt6:

1. **`QVariant(0)`** вместо `QVariant(QVariant::Int)`
    
2. **`QVariant(0.0)`** вместо `QVariant(QVariant::Double)`
    
3. **`QVariant(0LL)`** вместо `QVariant(QVariant::LongLong)`
    
4. Для строк можно оставить `""` или использовать `QString()`
    

Эти исправления устранят ошибки компиляции в Qt6.
## 🔄 Таблица замен для Qt6

|Было в Qt5|Стало в Qt6|Примечание|
|---|---|---|
|`QVariant(QVariant::Int)`|`QVariant(0)` или `QVariant::fromValue<int>(0)`|`int` со значением 0|
|`QVariant(QVariant::Double)`|`QVariant(0.0)` или `QVariant::fromValue<double>(0.0)`|`double` со значением 0.0|
|`QVariant(QVariant::LongLong)`|`QVariant(0LL)` или `QVariant::fromValue<qlonglong>(0)`|`long long` со значением 0|
|`QVariant(QVariant::Bool)`|`QVariant(false)`|Явное создание `bool`|
|`QVariant(QVariant::QString)`|`QString()` или `""`|Оба варианта работают|