```cpp

/// <param name="expression">Копия строки исходного параметрическое выражение.</param>

/// <param name="multiplePropertyValues">Множество указателей на различные словари свойств:

/// МТР, устройства, элемента устройства и др.</param>

/// <returns>Результат со строкой решённого текстового параметрического выражения.</returns>
MCC_API Result<std::string> ResolveExpression(std::string_view expression,
	const std::list<const std::map<std::string, PropertyValue, std::less<>>*>& multiplePropertyValues) const over
```