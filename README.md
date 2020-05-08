# Ouline
1. [**Chapter 1: General**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter1_General.md)     
	- Важные понятия и термины
	- General structure  
		- Вид типичного ситаксиса   
		- Общий цикл написания программы
	- Preprocessor -> compiler -> linker -> executable
		- Этапы преобразования .cpp -> .exe
		- Facts about compilation
		- Компиляция из терминала
	- Сегментация памяти в C++
		- Memory segments
			- Code (text) segment
			- Static segment
			- Stack segment
				- Последовательность заполнения стека
				- frame pointer он же base pointer он же ebp
	- Проблемы неоднозначности кода
	- Связи между объектами в C++

2. [**Chapter 2: Operators**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter2_Operators.md)     
	- Assiment and initialisation
	- Виды инициализации: прямая, uniform, копирующая
	- Оператор запятая (,)    
	- Тернарный if     
	- && (и) и || (или) - ленивые
	- Math    
		- Общее 
		- cmath
		- random number generator 
		
3. [**Chapter 3: Streams**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter3_Streams.md)   
	- std::cin
		- обработка некорректного ввода
		- getline(cin, str)	
	- std::cout
		- форматирование вывода
		- распадение в указатель при выводе
	- files
		- Работа с файлами, как с потоком (Formatted input)
		- Работа с файлом, как последовательностью символов char (Unformatted input)
			- Методы
		- State functions
		- binary mode
	- stringstream

4. [**Chapter 4: Memory**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter4_Memory.md)
	- Pointers
		- Указатель * и &
		- Размер
		- Scaling (Арифметика указателей)
		- null pointer
		- const pointers and pointers to const
		- Оператор доступа к членам класса через указатель (->)
		- Указатель типа void
		- Указатель на указатель
	- References
		- Общее: как и зачем?
		- Константные ссылки
	- Dynamic memory allocation
		- Статическое, автоматическое (стек) и динамическое выделение памяти (куча)
		- Когда следует выделять память в куче (динамически)
		- исключение bad_alloc
		- Memory leak
			

5. [**Chapter 5: Data types**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter5_Data_types.md)
	- Basic data types
		- Встроенные типы: размеры
		- char
		- Фиксированный размер int
		- float/double: ошибки округления
		- nan и inf
		- constants
		- auto
	- Анонимные переменные
	- Преобразование типов
		- Неявное преобразование данных
		- Явное преобразование данных
			- С style cast
			- C++ style cast
	- Regular arrays(фиксированные массивы)
		- объявление с enum 
		- Указатели и массивы
		- Память и индексация (array[i])
		- n-dimensional array
	- Dynamic arrays(Динамические массивы)
		- Выделение C-style
		- Выделение C++ style
		- многомерный динамический массив
	- C-style strings
		-C-style string symbolic constants
	- Enumeration
		- Обычный перечислитель
			- Scope, input/output, память
		- enum classes
		- Зачем это надо?
		- Short enum
	- Helpful functions
	
6.  [**Chapter 6: Classes**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter6_Classes.md)
	 - Общее
	 	- Initialization
	 - Struct (public class)
	 	- Присвоение значений полям
		- Вложенные структуры
		- Размер
	 - Class
	 	- Анатомия класса
		- Объявление методов вне класса
		- Конструктор
			- Implicitly generated (or default) constructor
			- Default constructors
			- Список инициализации членов класса
			- Инициализация константы
			- Инициализация массива
			- Инициализация всего подряд
			- Инициализация по умолчанию
			- Делегирующие конструкторы
		- Порядок действий в конструкторе
		- Копирующий конструктор
			- Определение конструктора копирования
			- Пример копирующего конструктора
			- Элизия 
			- Перегрузка оператора присваивания =
		- Неявное преобразования при вызове конструктора
			- Как запретить компилятору неявно преобразовывать аргументы: explicit, delete
		- Вложенные enum
		- Вложенные классы
		- Деструктор
			- Идиома RAII 
			- Порядок действий в деструкторе (инь и янь)
			- Implicitly generated (or default) destructor
		- Скрытый указатель *this ->
		- Const методы
			- mutable
			- Константные ссылки и классы
		- Static
			- Static переменные класса
			- Static методы
	- friend (дружественные класса и ф-ции)
		- Дружественные функции и несколько классов
		- Дружественные классы
	- Special member functions (implicitly generated functions)
	- void * или история про злого близница
	- Circular dependency (and forward declaration)

	
7. [**Chapter 7: Control Flow**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter7_Flow_Control.md)
	- Branches (Ветвление)
		- is else
		- Switch
	- Halts and jumps
	 	- goto
		- break и continue
 	- loops
		- for
		- while, while do

8. [**Chapter 8: Functions**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter8_Functions.md)
	- Термины 
	- Общее, main, void
		-Передача в main() аргументов из командной строки
			- Командная строка
	- Прототип функции
	- Аргументы
		- по значению
		- по ссылке (const и ф-ция как аргумент другой ф-ции)
		- по адресу (указателю)
		- передача массива в качестве аргумента
		- read only (const) pass
		- Страшная правда: все параметры-указатели
		- Default arguments
	- Возвращаемое значение
		- по значению 
		- по адресу (указателю)
		- по ссылке
	- inline (встроенные ф-ции)
	- Function overloading (перегрузка ф-ций)
		- Порядок сравнения параметров
	- Адрес ф-ции и указатель на нее
		- Инициализация и присвоение ф-ции указателем
		- Передача ф-ции в качестве параметра ф-ции
		- Псевдонимы для указателей на ф-ции
		- std::function
	- Эллипсис (ellipsis) аргумент

9. [**Chapter 9: Files and project magement**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter9_Files_and_project_manegment.md)
	- Добавление файла в проект
	- header file (.h)
		- Директивы препроцессора
		- header guards
	- Рефакторинг классов
		- Область принадлежности (::)
	
10. [**Chapter 10: Scopes**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter10_Scopes.md)
	- Scopes (Зоны видимости)
		- Локальные переменные, глобальные переменные (static, extern) 
		- сокрытие имен, глобальные ф-ции
	- namespace
		- Глобальное пространство имен 
		- :: и using
		- Псевдонимы (вложение)
11. [**Chapter 11: Algorithms lib**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter11_Algorithm_lib.md)
	- count(begin, end, what) 
	- sort(begin, end) 
	- swap(a, b)
	- set_difference(set1, set2, result)

12. [**Chapter 12: std data types**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter12_std_data_types.md)
	- strings
	- std::array
	- vector
		- Length vs Capacity
		- Методы
		- Методы делающие stack из std::vector
	- map и unordered_map
		- unordered_map
		- методы
		- в С++ 17
		- Снипет для подсчета повторений
	- set
		- методы
		- vector по set (и наоборот)
		- Оперции над множествами (сравнение и разница)
	- std::initializer_list
		
13.  [**Chapter 13: Exception handling**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/Chapter13_Exception_handling.md)    
	- exit() и cerr   
	- assert и static_assert
		- static_assert
		- Debuger vs release (NDEBUG)
		
14. [**Chapter 14: Operator overloading**](https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/Chapter14_Operator_overload.md)
	- Перегразка через обычные ф-ции
		- Перегрузка через дружественные ф-ции
		- Когда лучше использовать
	- Перегрузка через методы класса
		- Перегрузка симетричных операторов через методы класса
		- Когда лучше использовать
		- Создание цепочек операторов
		- Перегрузка унарных операторов
	- Перегрузка операторов сравнения
	- Перегрузка инкремента и декремента
	- Перегрузка операторов ввода и вывода
	- Перегрузка оператора индекса []
		- Указатели на объекты и перегруженный оператор []
		- Три совета
		- Упоротрая перегрузка [][]
	- Перегрузка оператора ()
		- Функторы
		- Где использвоать
	- Перегрузка операций преобразования типа
	- Перегрузка оператора ->
	- Перегрузка оператора присваивания =
		- Присваивание по-умолчанию  
		- Проблемы при работе с памятью  
		- Самоприсваивание, возврат *this
		- delete
		
15. [**Chapter 15: Inheritance**](https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/Chapter_15_Inheritance.md)
	- Общее
	- Порядок построения дочерних классов
		- Инициализация членов родительского класса при наследовании
		- Цепочка наследований
	- Приведение (преобразование) классов
	- Спецификаторы наследования
		- Спецификаторы доступа
		- Типы наследований
			- public 
			- private
			- protected
			- Тип наследования по дефолту (без явного указания)
			- Пример цепочки
	- Переопределение методов родительского класса 
		- Изменение спецификатора доступа при переопределении метода (и сокрытие метода)
	- Множественное наследование
		- Diamond of doom
		
49. 12. [**Chapter X: Useful STD Libraries**](https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/Chapter_X_Useful_%20STD_Libraries.md)
	- chrono
	- random (и cstdlib)

50. [**Chapter X: Other**](https://github.com/PlohoyParen/Cpp_doc/tree/master/Documents/ChapterX_Other.md)
	- Использование слова static
		- Global scope
		- Class 
