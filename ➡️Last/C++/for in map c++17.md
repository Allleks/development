
```cpp

std::map<int,std::string> mapa;

for(const auto& [key, value] : mapa){
	std::cout << key << ":" << value;
}
```