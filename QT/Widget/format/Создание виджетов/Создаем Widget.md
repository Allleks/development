1. ***Всегда указываем родителя!!!!*** `(иначе утечка памяти)`
```cpp
auto root = std::make_unique<QWidget>();
//child берем сырой указатель
QLable* child = new QLable("text",root.get());
```
2. Или добавляем в **layout** (он станет владельцем и удалит виджет)
3. *Корневой виджет* (QWidget/QMainWindow/QDialog) **без родителя через умные указатели**
___
### Родитель -> дети -> внуки
___
```cpp
QApplication app(argc, argv);

auto root = std::unique_ptr<QWidget>();         // корневой без родителя
QLabel* child = new QLabel("1",root.get());// ребенок root
QLabel* child2= new QLabel("2",root.get());// ребенок root
QLabel* grandchild = new QLabel("3", child);// ребенок child

return app.exec();  
//здесь удаляеться root и вся иерархия 
//1. удалиться child и далее grandchild
//2. удалиться child2
//3. удалиться root
}                 
```
### Production-ready
```cpp
#include <QAppliction>
#include <QMainWindow>
#include <QSatusBar>
#include <QMenuBar>
#include <QAction>
#include <memory>

class Application : public QApplication{
public:
	Application(int& argc, char** argv) : QApplication(argc, argv){
		setApplicationName("ProductionApp");
		setOrganizationName("MyCompany");
	}	
	int run(){
		auto mainWindow = createMainWindow();
		mainWindow->show();
		
		return exec();
	}
	
private:
	std::unique_ptr<QMainWindow> createMainWindow(){
		auto window = std::make_unique<QMainWindow>();
		window->setWindowTitle(applicationName());
		window->resize(1024,768);
		
		//menu
		auto* fileMenu = window->menuBar()->addMenu("&File");
		auto* exitAction = fileMenu->addAction("&Exit");
		
		QObject::connect(exitAction, &QAction::triggered,
		window.get(), &QMainWindow::close);
		
		window->statusBar()->showMessage("Ready");
		
		return window;
	}
};

int main(int argc, char* argv[]){
	Application app(argc,argv);
	return app.run();
}
```
___
# многопоточность -> 