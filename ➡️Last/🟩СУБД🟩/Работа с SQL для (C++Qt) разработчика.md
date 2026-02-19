**SQL (Structured Query Language)** — язык для общения с реляционными базами данных. Представь, что БД — это умный шкаф с папками, а SQL — команды библиотекарю: "положи", "найди", "исправь", "удали".
```text
┌─────────────────────────────────────┐
│          СУБД (PostgreSQL/MySQL)    │
│  ┌─────────────┐  ┌─────────────┐   │
│  │  Таблица 1  │  │  Таблица 2  │   │
│  │ ┌─────┬────┐│  │ ┌─────┬────┐│   │
│  │ │ ID  │Name││  │ │ ID  │City││   │
│  │ ├─────┼────┤│  │ ├─────┼────┤│   │
│  │ │ 1   │Иван││  │ │ 1   │МСК ││   │
│  │ └─────┴────┘│  │ └─────┴────┘│   │
│  └─────────────┘  └─────────────┘   │
└─────────────────────────────────────┘
         ▲              ▲
         │              │
    ┌────┴──────┬───────┴────┐
    │  SELECT   │   INSERT   │
    │  UPDATE   │   DELETE   │
    └───────────┴────────────┘
         Ваш SQL запрос
```
___
### Основные команды (CRUD):
```sql
-- 1. CREATE (создание таблицы)
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);

-- 2. INSERT (добавление данных)
INSERT INTO users (id, name, age) VALUES (1, 'Иван', 30);

-- 3. SELECT (чтение данных)
SELECT * FROM users WHERE age > 25;

-- 4. UPDATE (обновление)
UPDATE users SET age = 31 WHERE name = 'Иван';

-- 5. DELETE (удаление)
DELETE FROM users WHERE id = 1;
```
___
### Архитектура подключения Qt → SQL
```text
┌─────────────────────────────────────────────────────┐
│                    Ваше приложение                  │
│  ┌────────────────────────────────────────────────┐ │
│  │               C++/Qt код                       │ │
│  │ QSqlDatabase db = QSqlDatabase::addDatabase(); │ │
│  │  db.setHostName("localhost");                  │ │
│  │  db.setDatabaseName("mydb");                   │ │
│  └────────────────────────────────────────────────┘ │
│                          │                          │
│                          ▼                          │
│  ┌────────────────────────────────────────────────┐ │
│  │              Qt SQL Drivers                    │ │
│  │  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐           │ │
│  │  │QPSQL │ │QMYSQL│ │QSQLITE││QODBC │           │ │
│  │  └──────┘ └──────┘ └──────┘ └──────┘           │ │
│  └────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────┘
                          │
                          ▼
┌────────────────────────────────────────────────────┐
│              База данных (PostgreSQL/MySQL/SQLite) │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ │
│  │   Таблицы   │  │   Индексы   │  │   Вьюхи     │ │
│  └─────────────┘  └─────────────┘  └─────────────┘ │
└────────────────────────────────────────────────────┘
```
___
### Практические примеры для C++/Qt:
#### 1. Подключение к базе
```cpp
// .h файл
#include <QSqlDatabase>
#include <QSqlQuery>
#include <QSqlError>
#include <QVariant>
class DatabaseManager {
private:
    QSqlDatabase db;
    
public:
    bool connectToDatabase() {
        // PostgreSQL
        db = QSqlDatabase::addDatabase("QPSQL");
        db.setHostName("localhost");
        db.setPort(5432);
        db.setDatabaseName("Doodle_x_Angarsk");
        db.setUserName("alex");
        db.setPassword("password");
        
        if (!db.open()) {
            qDebug() << "Ошибка:" << db.lastError().text();
            return false;
        }
        return true;
    }
};
```
#### 2. Выполнение запросов (SELECT)
```cpp
// Чтение данных из таблицы Проемы
QVector<QString> getLocations() {
    QVector<QString> results;
    
    QSqlQuery query;
    query.prepare("SELECT [Место расположения проемов] FROM Проемы WHERE devID = :id");
    query.bindValue(":id", 1);  // защита от SQL injection!
    
    if (!query.exec()) {
        qDebug() << "Query error:" << query.lastError().text();
        return results;
    }
    
    while (query.next()) {
        QString location = query.value(0).toString();
        results.append(location);
    }
    
    return results;
}
```
#### 3. Вставка данных (INSERT)
```cpp
// Добавление новой записи
bool addOpening(int devId, const QString& location) {
    QSqlQuery query;
    query.prepare(
        "INSERT INTO Проемы (devID, [Место расположения проемов]) "
        "VALUES (:devId, :location)"
    );
    
    query.bindValue(":devId", devId);
    query.bindValue(":location", location);
    
    if (!query.exec()) {
        qDebug() << "Insert failed:" << query.lastError().text();
        return false;
    }
    
    // Получить последний вставленный ID
    int newId = query.lastInsertId().toInt();
    qDebug() << "Добавлена запись с ID:" << newId;
    
    return true;
}
```
#### 4. Транзакции (важно для целостности)
```cpp

// Несколько операций как одно целое
bool transferData() {
    QSqlDatabase::database().transaction();
    
    QSqlQuery query;
    bool success = true;
    
    success &= query.exec("UPDATE Проемы SET ...");
    success &= query.exec("INSERT INTO Лог ...");
    
    if (success) {
        QSqlDatabase::database().commit();
        qDebug() << "Транзакция зафиксирована";
    } else {
        QSqlDatabase::database().rollback();
        qDebug() << "Откат транзакции";
    }
    
    return success;
}
```
#### 5. Работа с моделью для QTableView
```cpp
// Отображение таблицы в UI
QSqlTableModel* model = new QSqlTableModel(this);
model->setTable("Проемы");
model->setFilter("devID = 1");  // WHERE условие
model->select();
// Настройка заголовков
model->setHeaderData(1, Qt::Horizontal, "Место расположения");
model->setHeaderData(2, Qt::Horizontal, "Тип проемов");
// Подключаем к view
ui->tableView->setModel(model);
ui->tableView->hideColumn(0);  // скрыть ID
```
### Схема работы с запросами в Qt:
```text

┌────────────────────────────────────┐
│         QSqlDatabase               │
│  (подключение к БД)                │
└────────────────────────────────────┘
              │
              ▼
┌────────────────────────────────────┐
│         QSqlQuery                  │
│  ┌────────────────────────────┐    │
│  │ query.prepare("SELECT...") │    │
│  │ query.bindValue(":id", 5)  │    │ ← защита от SQL injection
│  │ query.exec()               │    │
│  └────────────────────────────┘    │
└────────────────────────────────────┘
              │
              ▼
┌────────────────────────────────────┐
│      Результаты                    │
│  ┌────────────────────────────┐    │
│  │ while(query.next()) {      │    │
│  │   value = query.value(0);  │    │
│  │ }                          │    │
│  └────────────────────────────┘    │
└────────────────────────────────────┘
```
### Полезные ссылки на документацию:
- [Qt SQL C++ Classes](https://doc.qt.io/qt-6/sql-programming.html)
- [QSqlDatabase Class](https://doc.qt.io/qt-6/qsqldatabase.html)
- [QSqlQuery Class](https://doc.qt.io/qt-6/qsqlquery.html)
- [QSqlTableModel Class](https://doc.qt.io/qt-6/qsqltablemodel.html)
- [SQL Injection Prevention](https://doc.qt.io/qt-6/sql-sqlstatements.html#prepared-statements)