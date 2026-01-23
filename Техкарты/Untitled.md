```cpp
AbstractOperationsWindow (базовый класс)
    ├── OperationsGroupWindow (и другие окна операций)
    │       └── Кнопки вызывают EditExpressionWindow
    │
    └── EditExpressionWindow (окно редактора выражений)
            └── Нужно вернуть результат в вызывающее окно
```