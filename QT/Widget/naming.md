## 📊 **Таблица стандартных префиксов в Qt:**

|Виджет|Префикс|Пример|
|---|---|---|
|QPushButton|`btn`|`btnCalculate`|
|QTextEdit|`te`|`teExpression`|
|QLineEdit|`le`|`leUserName`|
|QLabel|`lbl`|`lblStatus`|
|QComboBox|`cb`|`cbLanguage`|
|QCheckBox|`chk`|`chkAutoSave`|
|QRadioButton|`rb`|`rbOptionA`|
|QSpinBox|`sb`|`sbAge`|
|QSlider|`sld`|`sldVolume`|
|QProgressBar|`prg`|`prgDownload`|
|QTabWidget|`tab`|`tabSettings`|

## 🎯 **Итоговые рекомендации:**
```cpp
// ВАШ СЛУЧАЙ:
// 1. QTextEdit для ввода выражения
teExpressionInput      // ← Используйте этот

// 2. QTextEdit для вывода результата  
teExpressionResult     // ← Используйте этот

// 3. Кнопка вычисления
btnCalculateExpression // ← Используйте этот
```
## ⚠️ **Чего избегать:**
- `textEdit1`, `textEdit2` — ничего не говорит
- `te1`, `te2` — слишком коротко
- `inputTextEdit` — неправильный порядок (префикс должен быть первым)
- `expressionTE` — префикс должен быть в начале