### 1. Проверяем БД на наличие необходимых полей и их добавление
* эти таблицы для свойств используются в comboBoxs
```
 Properties PropertiesValues
``` 
* в другие таблицы добавляем, свойство с одинаковым именем
* имена будут в 3 стадии - они одинаковые 
  1. для комбобокса в таблицах свойства для qt динамического свойства и в таблице бд куда будет добавляется эти свойства
     ![[дб.png]]
### 2. Добавляем в конструктор по названием полей
```cpp
#include "stdafx.h"
#include "Aperture.h"

Aperture::Aperture(long long devID) : Element(devID)
{
	baseParams.insert("Тип проемов", "");
	//Эти имена должны быть одинаковыми во всех 3 случаях или в двух если не нужен comboBox
	baseParams.insert("Расположение проема", "");
	baseParams.insert("Место расположения проемов", "");
	baseParams.insert("Длина", 0.0);
	baseParams.insert("Ширина", 0.0);
	baseParams.insert("Масса", 0.0);
	baseParams.insert("Кол-во", 0);
	baseParams.insert("Высота от уровня земли", 0.0);
	baseParams.insert("Высота от уровня площадки", 0.0);

	baseParams.insert("Крепление на петлях", false);
	baseParams.insert("Наличие кирпичной кладки", false);
	baseParams.insert("Толщина утеплителя", 0.0);

	baseParams.insert("Параметрическое обозначение болтов", "");
	baseParams.insert("НТД крепежа", "");
	baseParams.insert("Параметрическое обозначение гаек", "");
	baseParams.insert("НТД гаек", "");
	baseParams.insert("Количество болтов", 0);

	elementsName = "Проемы";

	simpleSelectParams = "ID, devID, Длина, Ширина, [Высота от уровня земли], [Высота от уровня площадки], [Тип проемов]";
}
```
___
## Где заполняется location_comboBox

В конструкторе `ApertureUI` вызываются два ключевых метода:
```cpp
fillComboBoxs_tab(ui->main_gridLayout);  // <- ЗАПОЛНЕНИЕ КОМБОБОКСОВ
fillCompletters_tab(ui->main_gridLayout);
```

QBaseElementUI -> элементы сохраняются и загружаются из базы изучит еще тонкости. Но я видел что заполнение и сохранение идет по слою QLayout то есть проходимся по всем элементам слоя. Интересный паттерн?