```cpp
#include "stdafx.h"
#include "abstract_operations_window.h"
#include "edit_expression_window.h"

AbstractOperationsWindow::AbstractOperationsWindow(QWidget* parent) : QWidget(parent)
{
    this->setAttribute(Qt::WA_DeleteOnClose);

    auto escapeShortcut = new QShortcut(QKeySequence(Qt::Key_Escape), this);

    connect(escapeShortcut, &QShortcut::activated, this, [this]() { this->close(); });
}

void AbstractOperationsWindow::showEditExpressionWindow(const QString& windowTitle)
{
    try
    {
        auto editWindow = new EditExpressionWindow(windowTitle);

        editWindow->show();
    }
    catch (const std::exception& ex)
    {
        qCritical() << ex.what();
    }
}

void AbstractOperationsWindow::closeEvent(QCloseEvent* event)
{
    try
    {
        auto messageBoxAnswer = QMessageBox::question(this,
            "Добавить сохранить данный элемент",
            "Добавить или сохранить изменения в данном элементе?",
            QMessageBox::Yes | QMessageBox::No | QMessageBox::Cancel);

        if (messageBoxAnswer == QMessageBox::Cancel)
        {
            event->ignore();

            return;
        }

        if (messageBoxAnswer == QMessageBox::No)
        {
            emit onClose(false);

            event->accept();

            return;
        }

        if (hasErrors())
        {
            QMessageBox::warning(this, windowTitle(), "Не все обязательные поля заполнены!");

            event->ignore();

            return;
        }

        saveResults();

        emit onClose(true);
    }
    catch (const std::exception& ex)
    {
        qCritical() << ex.what();

        emit onClose(false);
    }

    event->accept();
}

```