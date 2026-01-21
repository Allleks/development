[QString Class | Qt Core | Qt 6.10.1](https://doc.qt.io/qt-6/qstring.html)
____
## Detailed Description[](https://doc.qt.io/qt-6/qstring.html#details "Direct link to this headline")
QString stores a string of 16-bit [QChar](https://doc.qt.io/qt-6/qchar.html)s, where each [QChar](https://doc.qt.io/qt-6/qchar.html) corresponds to one UTF-16 code unit. (Unicode characters with code values above 65535 are stored using surrogate pairs, that is, two consecutive QChars.)
___
[Unicode](https://doc.qt.io/qt-6/unicode.html) is an international standard that supports most of the writing systems in use today. It is a superset of US-ASCII (ANSI X3.4-1986) and Latin-1 (ISO 8859-1), and all the US-ASCII/Latin-1 characters are available at the same code positions.
___
  QString uses [implicit sharing](https://doc.qt.io/qt-6/implicit-sharing.html) (copy-on-write) как и многие классы в Qt используют неявное совместное использование данных для максимизации использования ресурсов и минимизации копирования.
___
In addition to QString, Qt also provides the [QByteArray](https://doc.qt.io/qt-6/qbytearray.html) class to store raw bytes and traditional 8-bit '\0'-terminated strings. For most purposes, QString is the class you want to use. It is used throughout the Qt API, and the Unicode support ensures that your applications are easy to translate if you want to expand your application's market at some point. Two prominent cases where [QByteArray](https://doc.qt.io/qt-6/qbytearray.html) is appropriate are when you need to store raw binary data, and when memory conservation is critical (like in embedded systems).
___
### Initializing a string
One way to initialize a QString is to pass a `const char *` to its constructor. For example, the following code creates a QString of size 5 containing the data "Hello":
```cpp
QString str = "Hello";
```
___
QString converts the `const char *` data into Unicode using the [fromUtf8](https://doc.qt.io/qt-6/qstring.html#fromUtf8)() function.
___
In all of the QString functions that take `const char *` parameters, the `const char *` is interpreted as a classic C-style `'\\0'`-terminated string. Except where the function's name overtly indicates some other encoding, such `const char *` parameters are assumed to be encoded in UTF-8.
___
You can also provide string data as an array of QChars:
```cpp
static const QChar data[4] = { 0x0055, 0x006e, 0x10e3, 0x03a3 };
QString str(data, 4);
```
QString makes a deep copy of the [QChar](https://doc.qt.io/qt-6/qchar.html) data, so you can modify it later without experiencing side effects. You can avoid taking a deep copy of the character data by using [QStringView](https://doc.qt.io/qt-6/qstringview.html) or [QString::fromRawData](https://doc.qt.io/qt-6/qstring.html#fromRawData)() instead.
____
Another approach is to set the size of the string using [resize](https://doc.qt.io/qt-6/qstring.html#resize)() and to initialize the data character per character. QString uses 0-based indexes, just like C++ arrays. To access the character at a particular index position, you can use [operator[]](https://doc.qt.io/qt-6/qstring.html#operator-5b-5d)(). On non-`const` strings, [operator[]](https://doc.qt.io/qt-6/qstring.html#operator-5b-5d)() returns a reference to a character that can be used on the left side of an assignment. For example:
```cpp
QString str;
str.resize(4);

str[0] = QChar('U');
str[1] = QChar('n');
str[2] = QChar(0x10e3);
str[3] = QChar(0x03a3);
```
___
For read-only access, an alternative syntax is to use the *at()* function:
```cpp
QString str;

for (qsizetype i = 0; i < str.size(); ++i) {
    if (str.at(i) >= QChar('a') && str.at(i) <= QChar('f'))
        qDebug() << "Found character in range [a-f]";
}
```

The [at](https://doc.qt.io/qt-6/qstring.html#at)() function can be faster than [operator[]](https://doc.qt.io/qt-6/qstring.html#operator-5b-5d)() because it never causes a [deep copy](https://doc.qt.io/qt-6/implicit-sharing.html#deep-copy) to occur. Alternatively, use the [first](https://doc.qt.io/qt-6/qstring.html#first)(), [last](https://doc.qt.io/qt-6/qstring.html#last)(), or [sliced](https://doc.qt.io/qt-6/qstring.html#sliced)() functions to extract several characters at a time.
>Функция **at[]** может работать быстрее, чем **operator[]**, поскольку она никогда не приводит к глубокому копированию. В качестве альтернативы можно использовать функции , **first()**, **last()** или **sliced()**, для извлечения нескольких символов одновременно.

A QString can embed '\0' characters ([QChar::Null](https://doc.qt.io/qt-6/qchar.html#SpecialCharacter-enum)). The [size](https://doc.qt.io/qt-6/qstring.html#size)() function always returns the size of the whole string, including embedded '\0' characters.
>QString может содержать символы '\0' ([QChar::Null](https://doc.qt.io/qt-6/qchar.html#SpecialCharacter-enum)). Функция [size](https://doc.qt.io/qt-6/qstring.html#size)() всегда возвращает размер всей строки, включая встроенные символы '\0'.

After a call to the [resize](https://doc.qt.io/qt-6/qstring.html#resize)() function, newly allocated characters have undefined values. To set all the characters in the string to a particular value, use the [fill](https://doc.qt.io/qt-6/qstring.html#fill)() function.
>После вызова функции **resize()** вновь выделенные символы будут иметь неопределенные значения. Чтобы присвоить всем символам в строке определенное значение, используйте команду **fill()**.
___

QString provides dozens of overloads designed to simplify string usage. For example, if you want to compare a QString with a string literal, you can write code like this and it will work as expected:
```cpp
QString str;

if (str == "auto" || str == "extern"
        || str == "static" || str == "register") {
    // ...
}
```
You can also pass string literals to functions that take QStrings as arguments, invoking the QString(const char *) constructor.* Similarly, you can pass a QString to a function that takes a `const char *` argument using the [qPrintable](https://doc.qt.io/qt-6/qstring.html#qPrintable)() macro, which returns the given QString as a `const char *`. This is equivalent to calling [toLocal8Bit](https://doc.qt.io/qt-6/qstring.html#toLocal8Bit)().[constData](https://doc.qt.io/qt-6/qbytearray.html#constData)() on the QString.*
___
### Manipulating string data
QString provides the following basic functions for modifying the character data: [append](https://doc.qt.io/qt-6/qstring.html#append)(), [prepend](https://doc.qt.io/qt-6/qstring.html#prepend)(), [insert](https://doc.qt.io/qt-6/qstring.html#insert)(), [replace](https://doc.qt.io/qt-6/qstring.html#replace)(), and [remove](https://doc.qt.io/qt-6/qstring.html#remove)(). For example:
```cpp
QString str = "and";
str.prepend("rock ");     // str == "rock and"
str.append(" roll");        // str == "rock and roll"
str.replace(5, 3, "&");   // str == "rock & roll"
```