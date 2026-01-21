```cpp
#include <QTabWidget>
#include <QTextEdit>
#include <QPushButton>
#include <QVBoxLayout>
#include <QMessageBox>

class NotesApp : public QWidget
{
    Q_OBJECT
    
public:
    NotesApp(QWidget* parent = nullptr) : QWidget(parent)
    {
        // Создаем TabWidget
        m_tabWidget = new QTabWidget(this);
        m_tabWidget->setTabsClosable(true);
        m_tabWidget->setMovable(true);
        
        // Кнопка "Добавить заметку"
        QPushButton* addButton = new QPushButton("+ Новая заметка", this);
        
        // Размещаем на форме
        QVBoxLayout* layout = new QVBoxLayout(this);
        layout->addWidget(m_tabWidget);
        layout->addWidget(addButton);
        
        // Подключаем кнопку
        connect(addButton, &QPushButton::clicked, 
                this, &NotesApp::addNewNote);
        
        // Подключаем закрытие вкладок
        connect(m_tabWidget, &QTabWidget::tabCloseRequested,
                this, &NotesApp::onTabCloseRequested);
        
        // Добавляем первую заметку
        addNewNote();
    }
    
private slots:
    void addNewNote()
    {
        // Создаем текстовый редактор для заметки
        QTextEdit* textEdit = new QTextEdit();
        textEdit->setPlaceholderText("Введите текст заметки...");
        
        // Добавляем вкладку
        int index = m_tabWidget->addTab(textEdit, 
            QString("Заметка %1").arg(m_tabWidget->count() + 1));
        
        // Активируем новую вкладку
        m_tabWidget->setCurrentIndex(index);
        
        // Фокус на редакторе
        textEdit->setFocus();
    }
    
    void onTabCloseRequested(int index)
    {
        // Получаем редактор из вкладки
        QTextEdit* editor = qobject_cast<QTextEdit*>(m_tabWidget->widget(index));
        
        if (editor && !editor->toPlainText().isEmpty()) {
            // Спрашиваем подтверждение
            QMessageBox::StandardButton reply = QMessageBox::question(
                this,
                "Подтверждение",
                "Удалить заметку?",
                QMessageBox::Yes | QMessageBox::No
            );
            
            if (reply == QMessageBox::No) {
                return; // Не удаляем
            }
        }
        
        // Удаляем вкладку
        m_tabWidget->removeTab(index);
        
        // Если вкладок не осталось - создаем новую
        if (m_tabWidget->count() == 0) {
            addNewNote();
        }
    }
    
private:
    QTabWidget* m_tabWidget;
};
```
### Паттерн 1: Одна вкладка = один документ
```cpp
class DocumentTabWidget : public QTabWidget
{
public:
    void openDocument(const QString& filePath)
    {
        DocumentWidget* doc = new DocumentWidget(filePath);
        int index = addTab(doc, QFileInfo(filePath).fileName());
        setCurrentIndex(index);
    }
};
```
### Паттерн 2: Вкладки как инструменты
```cpp
class ToolboxWidget : public QTabWidget  
{
public:
    ToolboxWidget()
    {
        addTab(new CalculatorWidget(), "Калькулятор");
        addTab(new ConverterWidget(), "Конвертер");
        addTab(new NotesWidget(), "Заметки");
        
        // Только одна активная вкладка
        setTabPosition(QTabWidget::West);
    }
};
```
### Паттерн 3: Рабочее пространство (как в IDE)
```cpp
class WorkspaceWidget : public QTabWidget
{
public:
    WorkspaceWidget()
    {
        setTabsClosable(true);
        setMovable(true);
        setDocumentMode(true); // Современный стиль
        
        // Добавляем кнопку "+" для новых вкладок
        QToolButton* addButton = new QToolButton();
        addButton->setText("+");
        setCornerWidget(addButton, Qt::TopLeftCorner);
        
        connect(addButton, &QToolButton::clicked, 
                this, &WorkspaceWidget::createNewWorkspace);
    }
};
```
