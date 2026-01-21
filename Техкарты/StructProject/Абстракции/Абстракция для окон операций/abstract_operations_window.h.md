```cpp
#pragma once

/// <summary>
/// Класс абстрактного окна для редактирования элементов групп работ.
/// </summary>
class AbstractOperationsWindow : public QWidget
{
    Q_OBJECT

public:

    /// <summary>
    /// Создать абстрактное окно для работы с элементом группы работ.
    /// </summary>
    /// <param name="parent">Родительский виджет.</param>
    explicit AbstractOperationsWindow(QWidget* parent);

    /// <summary>
    /// Виртуальный деструктор для реализации наследования.
    /// </summary>
    virtual ~AbstractOperationsWindow() = default;

signals:

    /// <summary>
    /// Событие закрытия окна.
    /// </summary>
    /// <param name="parent">Закрытие окно произошло с подтверждением?</param>
    void onClose(bool isAccept);

protected:

    /// <summary>
    /// Проверить, есть ли ошибки в полях окна при сохранении результатов.
    /// </summary>
    /// <returns>True, если есть ошибка в одном из заполняемых полей.</returns>
    virtual bool hasErrors() const = 0;

    /// <summary>
    /// Сохранить данные из виджетов окна в изменяемый объект, с которым работает абстрактное окно.
    /// </summary>
    virtual void saveResults() = 0;

protected slots:

    /// <summary>
    /// Переопределенный метод закрытия окна создания / редактирования группы работ.
    /// </summary>
    /// <param name="event">Указатель на Qt объект события закрытия окна.</param>
    void closeEvent(QCloseEvent* event) override;

    /// <summary>
    /// Обработка нажатия на кнопку отображения редактора параметрических выражений.
    /// </summary>
    /// <param name="windowTitle">Заголовок окна, берется из QLabel вызывающих окон</param>
    void showEditExpressionWindow(const QString& windowTitle);
};
```