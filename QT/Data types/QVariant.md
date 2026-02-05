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