[QObject Class | Qt Core | Qt 6.10.1](https://doc.qt.io/qt-6/qobject.html)
Наследуемся если нам нужны Сигналы и слоты и система владения объектами и другие механизмы qt но не в GUIкоде. *Если нужен GUI то наследуемся от QWidget он наследуется от QObject*
```cpp
class My_class : public QObject{
	Q_OBJECT//макрос обязателен!! сигналы и слоты мета объект
	public:
}

QObject::connect();
```
___
Объекты QObject нельзя копировать(отключены конструкторы копирования и операторы присваивания)
___
muthithread ->[QObject Class | Qt Core | Qt 6.10.1](https://doc.qt.io/qt-6/qobject.html#deleteLater)