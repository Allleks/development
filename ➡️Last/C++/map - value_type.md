Что за метод у мапы? столкнулся с такой конструкции
**std::map::value_type**
```cpp 
using OpMap = std::map<std::string, std::pair<int, int>,std::less<>>;

OpMap::value_type assocs[] =
{
	OpMap::value_type("+", std::make_pair(0, LEFT_ASSOC)),
   OpMap::value_type("-", std::make_pair(0, LEFT_ASSOC)),
   OpMap::value_type("*", std::make_pair(5, LEFT_ASSOC)),
   OpMap::value_type("/", std::make_pair(5, LEFT_ASSOC)),
   OpMap::value_type("^", std::make_pair(10, LEFT_ASSOC))
};
```