# Chapter 5 Data types

## Basic data types   
1 byte = 8 bit = 2^8 = 256. So 1 byte is range of 0 to 255 (если без знака).  Размер определённых типов данных зависит от компилятора и/или архитектуры компьютера! C++ гарантирует только их минимальный размер (см. таблицу). 
Каждый байт имеет свой адрес. Т.е. если переменная весит 4 байта, то в памяти она "заняла" 4 адреса.
### Встроенные типы   
|Type|Size|range|
|:---|----:|:----|
|char| 1byte|-127 to 127 or 0 to 255|
|unsigned char*|	1byte|	0 to 255|
|signed char|	1byte	|-127 to 127
|int|	4bytes	|-2147483648 to 2147483647
|unsigned int*|	4bytes|	0 to 4294967295
|signed int	|4bytes	|-2147483648 to 2147483647
|short int|	2bytes	|-32768 to 32767
|unsigned short int*| -	|0 to 65,535
|signed short int|	-|	-32768 to 32767
|long int	|4bytes|	-2,147,483,648 to 2,147,483,647
|signed long int|	4bytes|	same as long int
|unsigned long int*|	4bytes|	0 to 4,294,967,295
|float|	4bytes|	+/- 3.4e +/- 38 (~7 digits)
|double|	8bytes|	+/- 1.7e +/- 308 (~15 digits)
|long double|	8bytes|	+/- 1.7e +/- 308 (~15 digits)|  

*Использование `unsigned` нежелательно.

**Other**
- `bool` - logic (1 byte) - 1 байт т.к. нужен адрес, а минимальный размер с адресом - 1 байт.
- `void` - empty data type (исп. в 1) ф-циях: `void function(){}` - ничего не возвращает, 2) в указателях)  

#### Prefixes 
1. `long ..` int (or double) - 4 bytes ($\pm 2*10^6$) (or 8 bytes) 
2. `sort ..` int - 2 bytes 
3. `unsigned ..` - only *positive*
4. `signed ..` - both *postive* and *negative*
5. `float` vs `double` (точность выше, чем у float) - 4 vs 8 bytes 
Объявлять можно просто префиксом: `short x = 10`, `long long y = 100` (int подразумевается, но явно не указывается).

### char
Целочисленный тип данных, т.е. хранит в себе int (-127 до 127). Это число конвертируется в ASCII (от англ. «American standard code for information interchange»). От 97 до 122 англ буквы. При этом не важно как ее инциализировать символом (`'a'`) или числом ASCII:
```cpp
char ch1(97); // инициализация переменной типа char целым числом 97
char ch2('a'); // инициализация переменной типа char символом 'a' (97)
```
Вывод будет по стандарту - символ. Чтобы вывести число, нужно конвертирова char -> int: 1й способ сохранить char в новую переменную типа int (не желательно), 2й способ использовать `static_cast<int>(ch)` (желательно).

**char16_t и char32_t**   
Нужны для использования других (не ASCII) символьных кодировок (напр., Unicode, который имеет в запасе более 110 000 целых чисел для представления символов из разных языков). Так, есть UTF-32 (требует 32 бита для представления символа, `char32_t`) и UTF-16 (требует 16 бит для представления символа, `char16_t`).

### Фиксированный размер int
Требуется подключить библеотеку `#include <cstdint>` стандартной библиотеки (не забудь namespace std/std::). 
1 байт(8 бит): `int8_t` (signed), `uint8_t` (unsigned).  
2 байта: `int16_t` (signed), `uint16_t`(unsigned).  
4 байта: `int32_t` (signed), `uint32_t` (unsigned).   
8 байт: `int64_t` (signed), `uint64_t` (unsigned).   
- Многие компиляторы автоматически переводят `std::int8_t` и `std::uint8_t` в `char`, будь аккуратен.  

### float/double: ошибки округления
Возьмем дробь 1/10. В десятичной системе счисления эту дробь можно представить как 0.1, в двоичной системе счисления эта дробь представлена в виде *бесконечной* последовательности: 0.00011001100110011… Именно из-за подобных разногласий в представлении чисел в разных системах счисления — у нас могут возникать проблемы с точностью. Например:
```cpp
#include <iostream>
#include <iomanip> // для std::setprecision()
 
int main()
{
    std::cout << std::setprecision(17);
 
    double d1(1.0);
    std::cout << d1 << std::endl;					  // выйдет 1
	
    double d2(0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1); // должно выйти 1.0
    std::cout << d2 << std::endl;					  // но выйдет 0.99999999999999989
}
```
Из за этого же есть проблемы со сравнением двух float чисел т.к. в примере выше 1.0 != 0.99999999999999989. Для этого можно использовать `epsilon ф-циию сравнения`:
```cpp
// Возвращаем true, если разница между a и b меньше absEpsilon или в пределах relEpsilon 
bool approximatelyEqualAbsRel(double a, double b, double absEpsilon, double relEpsilon)
{
    // Проверяем числа на их близость - это нужно в случаях, когда сравниваемые числа являются нулевыми или около нуля
    double diff = fabs(a - b);
    if (diff <= absEpsilon)
        return true;
 
    // В противном случае, возвращаемся к алгоритму Кнута («Искусство программирования, том 2: Получисленные алгоритмы» (1968))
    return diff <= ( (fabs(a) < fabs(b) ? fabs(b) : fabs(a)) * relEpsilon);
}
```

### nan и inf
Особые типы:   
`inf` (или «бесконечность», от англ «infinity»): может быть положительной и отрицательной.
`nan` (или ещё «не число», от англ «not a number»). 
```cpp
double zero = 0.0;
double posinf = 5.0 / zero; // положительная бесконечность 
std::cout << posinf << "\n";
 
double neginf = -5.0 / zero; // отрицательная бесконечность 
std::cout << neginf << "\n";
 
double nan = zero / zero; // не число (математически некорректно)
std::cout << nan << "\n";
```
### constants
Константы должны быть сразу объявлены (т.е. со значением). Нельзя сначала инициализировать, а потом присвоить значение.  
```cpp
const double gravity { 9.8 };   // предпочтительнее использовать const перед типом данных
int const sidesInSquare { 4 };  // ок, но вариант выше - лучше

g =  9.8 		        // можно инициализоровать переменной
const double gravity {g};
```
- Можно явно указать, что константа является константой времени компиляции: `constexpr int  = 1`. Это особо не нужно т.к. компилятор сам умеет определять тип константы. 

### auto
Ключевое слово auto использовалось для явного указания, что переменная должна иметь автоматическую продолжительность. Однако, поскольку все переменные в новых версиях C++ по умолчанию имеют автоматическую продолжительность, и, если явно не указывать другой тип продолжительности, то ключевое слово auto стало лишним и, следовательно, устаревшим. Начиная с C++11, ключевое слово auto при инициализации переменной может использоваться вместо типа переменной, чтобы сообщить компилятору, что он должен присвоить тип переменной исходя из инициализируемого значения. Можно использовать для:

1. Инициализации переменных: `auto num = 12;`. Т.к. компилятор определяет тип по присваиваему значние, то просто объявить с auto нельзя. 
2. Автоматическое определение типа возвращаемого значения функции (С++ 14 и выше):
	```cpp
	// это считаетяс плохой практикой
	auto subtract(int a, int b)
	{
	    return a - b;
	}
	```
3. В loop, где надо выводить содержание итерируемых контейнеров
	```cpp
	vector<int> nums= {1,2,3};
	for (auto c: nums){cout << c << ",";}
	```
4. Cинтаксис trailing
	```cpp
	auto subtract(int a, int b) -> int;
	auto divide(double a, double b) -> double;
	auto printThis() -> void;
	```	

## Анонимныe объекты
**Анонимный объект** — это значение без имени. Поскольку имени нет, то и способа ссылаться на этот объект за пределами места, где он создан — тоже нет. Следовательно, анонимные объекты имеют область видимости выражения и они создаются, обрабатываются и уничтожаются в пределах одного выражения.
```cpp
int plus(int a, int b)
{
    return a + b; // анонимный объект создаётся для хранения и возврата результата выражения a + b
}

/* Пусть есть некоторый класс Dollars */
Dollars dollars(7); // обычный объект класса
Dollars(8); // анонимный объект класса

Dollars add(const Dollars &d1, const Dollars &d2)
{
    return Dollars(d1.getDollars() + d2.getDollars()); // возвращаем анонимный объект класса Dollars
}
 
int main()
{
    std::cout << "I have " << add(Dollars(7), Dollars(9)) << " dollars." << std::endl; // выводим анонимный объект класса Dollars
 
    return 0;
}
```

## Преобразование типов 
Есть два типа преобразования типов данных в C++: неявное и явное.   

### Неявные преобразования
1. **Неявное преобразование данных** -компилятор автоматически преобразовывает данные, чтобы корректо с ними работать. Есть несколько уровней неявных (автоматических) преобразований:
- **Числовое расширение** - это когда значение из одного типа данных конвертируется в другой тип данных побольше (по размеру и по диапазону значений). Так типы меньше, чем `int` (`bool`, `char`, `short int` (или просто `short`), включая их unsigned и signed версии) расширяется в `int`. Также `float` конвертируется в `double`. Например, 
	```cpp
	long l(65); // расширяем значение типа int (65) в тип long
	double d(0.11f); // расширяем значение типа float (0.11) в тип double
	```
При числовом расширении данные не теряются из-за переполнения.
- **Числовые конверсии** -  когда конвертируем значение из более крупного типа данных в аналогичный, но более мелкий тип данных (напр., `int` в `short`, `float` в `double`), или конвертация происходит между разными типами данных (напр., `int` в `char`). Например,
	```cpp
	// без переполнения и потери данных
	double d = 4; // конвертируем 4 (тип int) в double
	short s = 3;  // конвертируем 3 (тип int) в short
	// произойлет переполнение
	int i = 30000;
	char c = i;   // т.к. char (-127, 127), то произойдет переполнение. Результат будет 48 
	// еще переполнение 
	float f = 0.123456789; // значение типа double - 0.123456789 имеет 9 значащих цифр, но float может хранить только 7
	```
- **Обработка арифметических выражений** Арифметические операторы требуют, чтобы их операнды были одного типа данных. Для этого преобразует один или оба операнда по правилам: 1) Если операндом является целое число меньше (по размеру/диапазону) типа int, то оно подвергается интегральному расширению в int или в unsigned int. 2)Если операнды разных типов данных, то компилятор вычисляет операнд с наивысшим приоритетом и неявно конвертирует тип другого операнда в соответствие к типу первого.
	```cpp
	double a(3.0);
	short b(2);
	std::cout << a + b << std::endl; // тип данных в выражения (a + b) - double, при этом b будет конвертирован short -> double
	```	
### Явные преобразования   
#### C style cast 
```cpp
int i1 = 11;
int i2 = 3;
float x = (float)i1 / i2;
// или то же свмое
float x = float(i1) / i2;
```
Не рекомендуется использовать C-style т.к. он может поломать тип `const` переменных и все поломать.
#### C++ style cast
##### static_cast
Используется для преобразования базовых типов     
```cpp
char c = 97;
std::cout << static_cast<int>(c) << std::endl; // в результате выведется 97, а не 'a'
```
##### reinterpret_cast
Используется для преобразования указателе. **!Рекомендуется избегать**. В принципе, только преобразование к указателю на char безопасно, для других надо быть супер аккуратным.
```cpp
int a = 69;
char* cptr = reinterpret_cast<char*>(&a);
```

##### Что C++ стандарт думает об этом
The C++ standard guarantees the following:       
`static_cast`ing a pointer to and from `void*` preserves the address. That is, in the following, `a`, `b` and `c` all point to the same address:
```cpp
int* a = new int();
void* b = static_cast<void*>(a);
int* c = static_cast<int*>(b);
```
     
`reinterpret_cast` only guarantees that if you cast a pointer to a different type, and then reinterpret_cast it back to the original type, you get the original value. So in the following:
```cpp
int* a = new int();
void* b = reinterpret_cast<void*>(a);
int* c = reinterpret_cast<int*>(b);
```
`a` and `c` contain the same value, but the value of `b` is unspecified. (in practice it will typically contain the same address as a and c, but that's not specified in the standard, and it may not be true on machines with more complex memory systems.)

For casting to and from `void*`, `static_cast` should be preferred.

## typedef (alias)
`typedef double time_t;` - стейтмет позволяющтий использовать псевдоним (alias) для типа данных. Тип данных при этом остается тем же (в прим., `double`), но для удобства чтения/написания кода будет использлваться новое имя будто это новый тип (в прим.,`time_t`). Например,
```cpp
/* тип данных  std::vector<std::pair<std::string, int> >   заменен на имя по-проще */ 
typedef std::vector<std::pair<std::string, int> > pairlist_t; // используем pairlist_t в качестве псевдонима 

pairlist_t pairlist; // объявляем pairlist_t
 
boolean hasAttribute(pairlist_t pairlist) // используем pairlist_t в качестве параметра функции
{
    // Что-то делаем
}

```
Два эквивалентных способа переименования:
```cpp
typedef double time_t; // используем time_t в качестве псевдонима для типа double
using time_t = double; // используем time_t в качестве псевдонима для типа double
```

    
## Regular and N-dimetional arrays
### Объявление и заполнение 
```cpp
int array[5] = { 0, 1, 2, 3, 4 }; // явно указываем длину массива через список инициализаторов 
int array[] = { 0, 1, 2, 3, 4 }; // список инициализаторов автоматически определит длину массива
```
Автоматическое заполнение массива нулями: если при инициализации задан размера массива, список инициализаторов не полный, то компилятор автоматически заполнит остаток нулями:
```cpp
int ones_array[5] = { 5, 7, 9 }; 	// Инициализируем только первых 3 элемента, остальное нули
int second_array[5] = { }; 		// Инициализируем все элементы массива значением 0

int third_array[5] { 4, 5, 8, 9, 12 };	// используем uniform-инициализацию для инициализации фиксированного массива
```
Массивы может состоять из элементов любого типа (как стандартных, так и пользовательских). Например, массив из `struct`:
```cpp
struct Rectangle
{
    int length;
    int width;
};
Rectangle rects[4]; // объявляем массив с 4-ма прямоугольниками
```

**Объявление с помощью перечислений (enum)**    
Т.к. индексы массива - это только int числа, то можно сделать связку array + enum для того, чтобы иметь именные индексы:
```cpp
enum StudentNames
{
    SMITH, // 0
    ANDREW, // 1
    IVAN, // 2
    JOHN, // 3
    ANTON, // 4
    MAX_STUDENTS // 5 		// Вводится, чтобы удобно было создавать массив (т.к. нумерация с 0)
};
 
int main()
{
    int testScores[MAX_STUDENTS]; // всего 5 студентов
    testScores[JOHN] = 65;
}
```
Если использовать `enum class` (чтобы иметь область виденья внутри enum), то индексы надо будет вручную кокнвертировать из `int testScores[StudentNames::JOHN] = 65;` в `testScores[static_cast<int>(StudentNames::JOHN)] = 65;`. Проще избегать класса enum, и просто вставить его в свой собственный namespace:
```cpp
namespace StudentNames
{
    enum StudentNames
    {
        SMITH, // 0
        ANDREW, // 1
        IVAN, // 2
        JOHN, // 3
        ANTON, // 4
        MISHA, // 5
        MAX_STUDENTS // 6
    };
}
 
int main()
{
    int testScores[StudentNames::MAX_STUDENTS]; // всего 6 студентов
    testScores[StudentNames::JOHN] = 65;
}
```

**Дополнительно**   
1. *(Внимание!)* В С++ нет проверки на диапозон индексирования, поэтому компилятор не заметит, если выйти за размеры массива. В этом случаи он начнет переписывать учстки памяти занятые другими переменными и все станет оч плохо:
	```cpp
	// компилятор не заметит этого, и ошибки не будет! 
	int array[5]; // массив содержит 5 простых чисел
	array[5] = 14;
	```
2. *(Можно не париться)* При объявлении массива фиксированного размера, его длина (между квадратными скобками) должна быть константой типа **compile-time** (которая определяется во время компиляции). **Хотя, большинство компиляторов ругаться не будут**. Например,
	```cpp
	const int length = 4;
	int array[length]; 	// хорошо length - compile time

	/* На самом деле можно не заморачиваться, т.к. большинство компиляторов ругаться не будут
	и все будет работать корректно */
	int length = 4;
	int array[length]; 	// плохо length не compile time

	int width;
	std::cin >> width;
	int array[width]; 	// плохо: width должна быть константой типа compile-time!

	// Используем константную переменную типа runtime
	int temp = 8;
	const int width = temp;
	int array[width];	// плохо: здесь width является константой типа runtime, но должна быть константой типа compile-time!
	```
### Указатели и массивы
При объявлении массива (напр., `int my_array[4] = { 5, 8, 6, 4 };`) в переменную my_array сохраняется адрес первого элемента массива. Сама переменная имеет тип `int[4]`. Это создает принципиальное отличиие переменной массива от простого указателя на первый элемент (тип которого будет `int *`). Тип `int[4]` - несет в себе адрес 1го элемента и длинну массива, а также она знает, что это массивю. Тип`int *` - это просто указатель на первый int из массива (он не знает ни длинну, ни факт, что это массив). При некоторых действиях (напр., передача в ф-ции) массива **распадается (decay)** (т.е. неявно преобразовывается компилятором) из `int[4]` в указатель на первый элемент `int *`.
```cpp
int my_array[4] = { 5, 8, 6, 4 };
// Выводим значение массива (переменной array)
std::cout << "The my_array has address: " << my_array << '\n';	// The array has address: 004BF968
// Выводим адрес элемента массива
std::cout << "Element 0 has address: " << &my_array[0] << '\n';	// Element 0 has address: 004BF968
// my_array распадается в указатель на превый элемент
int *ptr = my_array;
std::cout << "Element 0 has address: " << prt;			// Element 0 has address: 004BF968

std::cout << *my_array; 	// выведется 5
std::cout << *ptr; 		// выведется  5
/* Адрес будет совпадать, однако компилятор будет рассматривать их по разному: см. ниже */
```
Принципиальное отличие между указателем на первый элемент (`&array` и `ptr`) и самой переменной `my_array` то, что компилятор рассматривает их как разные типы данных. Например, 
```cpp
int array[4] = { 5, 8, 6, 4 };
std::cout << sizeof(array) << '\n'; 	// выведется sizeof(int) * длина array (выведет: 16)
 
int *ptr = array;
std::cout << sizeof(ptr) << '\n'; 	// выведется размер указателя, т.е. адреса памяти в байтах (выведет: 4)
```
### Память и индексация (array[i]) 
На самом деле, элементы массива имеют последовательные адреса памяти. Т.е.: 
```cpp
int array[] = { 7, 8, 2, 4, 5 };
 
std::cout << "Element 0 is at address: " << &array[0] << '\n';	// Element 0 is at address: 002CF6F4
std::cout << "Element 1 is at address: " << &array[1] << '\n';	// Element 1 is at address: 002CF6F8
std::cout << "Element 2 is at address: " << &array[2] << '\n';	// Element 2 is at address: 002CF6FC
std::cout << "Element 3 is at address: " << &array[3] << '\n';	// Element 3 is at address: 002CF700
// т.е. различаются все на 4 байта (рамер int)
```
Так как оперция `ptr + 1` выдает адрес следующего элемента того же типа, что и указатель `ptr` (см. **арифметика указателей**), то индексация массива (`array[i]`) - это просто псевдоним для арифметики над первым элементом массива т.е.: `array[i]` — это то же самое, что и `*(array + i)` (где i - индекс массива, `*()`- разименование элемента памяти с адресом `array + i` (т.е. первый элемент + i).

```cpp
m[i] == *(m + i);	//это то, что неявно делает компилятор
```
Тк это то, что неявно делает компилятор, то работа с массивами через арифметику указателей на самом деле быстрее, чем через `[]`.
```cpp
 std::cout << &array[1] << '\n'; 	// выведется: 001AFE74 (адрес памяти элемента под номером 1)
 std::cout << array+1 << '\n'; 		// выведется: 001AFE74 (адрес памяти указателя на массив + 1) 
 
 std::cout << array[1] << '\n'; 	// выведется: 8
 std::cout << *(array+1) << '\n'; 	// выведется: 8 (обратите внимание на скобки, они здесь обязательны)
```

### Передача массива в функции
(см. главу про функции, раздел аргументы)

### Sizeof of array
Общий размер массива - это длина массива умножена на размер одного элемента (в байтах). Надо быть аккуратным при использовании оператора `sizeof`:
```cpp
void printSize(int array[])
{
    std::cout << sizeof(array) << '\n'; 	/* выводится размер указателя, а не массива
    т.к. в ф-цию передается указатель на массива, а не сам массив */
}
 
int main()
{
    int array[] = { 1, 3, 3, 4, 5, 9, 14, 17 };
    std::cout << sizeof(array) << '\n'; 	// выводится размер массива
    printSize(array);				// в ф-цию передается указатель на массив	
}
```
Как найти размер массива, длина которого не была явно указана? Так:
```cpp
int array[] = { 1, 3, 3, 4, 5, 9, 14, 17 };
std::cout << "The array has: " << sizeof(array) / sizeof(array[0]) << " elements\n";
```
### n-dimensional array
#### Объявление и присвоение
В двумерном массиве первый (левый) индекс принято читать как количество строк, а второй (правый) как количество столбцов:
```cpp
int array[2][4];   // 2-элементный массив из 4-элементных массивов
array[1][3] = 7;   // без приставки int (типа данных)
```
**Инициализация** 
```cpp
int array[3][5] =
{			// обрати внимание на внешние {}
{ 1, 2, 3, 4, 5 },	// строка №0
{ 6, 7, 8, 9, 10 },	// строка №1
{ 8, 9, 11}		/* строка №2 = 8, 9, 11, 0, 0 - тут
			так же работает автозаполнение нулями */
};

// Заполнение нулями
int array[3][5] = {};
```
В двумерном массиве со списком инициализаторов можно не указывать только левый индекс (авто определение длинны):
```cpp
// тут все ОК
int array[][5] =
{			
{ 1, 2, 3, 4, 5 },
{ 6, 7, 8, 9, 10 },
{ 11, 12, 13, 14, 15 }
};

// тут будет ошибка
int array[][] = 
{
{ 3, 4, 7, 8 },
{ 1, 2, 6, 9 }
};
```

Память для многомерных массивов на стеке (не динамичесикх) на самом деле выделятся последовательно. Те для массива m[4][5] на самом деле выделсятеся кусок последовательной памяти размера 20. В связи с этим можно применять инициализацию вида `int array[][5]`, но не `int array[4][]` так тут просто говорится: "нужно 5 стобцов, соответсвенно, надо поделить все данные мне элементы на 5". Более того:
```cpp
int a[][3] = { {1, 2, 3}, {4, 5, 6}, {7, 8, 9} };
//то же самое что и
int a[][3] = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
//компилятор сам поделить все на 3 стобца
```

```cpp
int a[N][M]; 			//псть есть массив a[N][M]:
a[i][j] = 1; 			//это то же самом что и 
*((int *)a + i * M + j) = 1;	/*a - указател на начало массива,
i*M - это мы переступаем через i стобцов размера M, и приходим к элементу j */
```

## Dynamic arrays
### Выделение C-style 
Функции для управления памятью в стиле C:
```cpp
void * malloc ( size_t size);			//просто выделяет кусок памяти
void * calloc ( size_t nmemb , size_t size);	//выделяет память под массив
void * realloc( void * ptr , size_t size);	//ищменяет размер уже выделенной памяти
void free ( void * ptr);			//возвращает выделенную память
```
- `size_t` — специальный целочисленный беззнаковый тип, может вместить в себя размер любого типа в байтах. Он используется для указания размеров типов данных, для индексации массивов и пр.
- `void *` — это указатель на нетипизированную память. Все ф-ции выше работают с указателями на void: `malloc()` и `calloc()` возвращают его, `free()` - принимает, а `realloc()` и принимает, и возвращает.   
- Когда выделяется память используя одну из ф-ций выше, на самом деле резервируется чуть больше памяти, чем запрошено. Во-первых, в начале сегмета выделяется память, куда записывается служебная информация (например, размер выделенной памяти). Поэтому при освобождении памяти с помощью ф-ции `free()` достаточно указателя, и не надо указывать размер памяти. Во-вторых, обычно размер округляется до кратного 16 байт (мне неведомо зачем). 

1. `malloc()` - выделяет область памяти размера  size. При этом, данные не инициализируются (наполнены мусором).
2. `calloc()` — выделяет массив из nmemb (кол-во членов) размера size. Данные инициализируются нулём.
3. `realloc()` — изменяет размер области памяти по указателю `ptr` на `size`. Это делается на месте, то есть прибавляется еще участок памяти, если он свободен. В этом случаии назад возвращается тот же указатель, что был передан. Если не достаточно свободной памяти последовательно к ячейкам `ptr`, то ф-ция найдет новое место, скопирует туда данные и вернет новый указатель на этот участок памяти. 
3. `free()` - • free — освобождает область памяти, ранее выделенную одной из функций malloc/calloc/realloc.

```cpp
// создание массива из 1000 int
int *m=(int *)malloc (1000 * sizeof ( int ));
m[10] = 10;
// изменение размера массива до 2000
m=(int *)realloc(m, 2000 * sizeof (int ));
// освобождение массива
free(m);
// создание массива нулей
m=(int *)calloc(3000, sizeof (int ));
free(m);
m = 0;
```
- Так как все эти ф-ции возвращают указатель на void, то нужно сделать преобразование типа, чтобы работать с целочисленными указателем (как с обычным массивом): `(int *)`. Теперь обращаться к указателю можно, используя стандартный синтаксис для массива: `m[10] = 10;`.
- При передаче указателя в ф-ции `free()` и `realloc()` любой указатель неявно преобразуется в указатель на тип void: `free(m)` (а не `free((void* )m)`).

### Выделение C++ style 
Для выделения динамического массива используется отдельная форма операторов `new` и `delete` для работы с массивами: `new[]` и `delete[]`. Можно вместо `new[]` использовать просто `new` (для `delete[]` нельзя), т.к. компилятор сам применит нужный оператор из контекста.
```cpp
std::cout << "Enter a positive integer: ";
int length;
std::cin >> length;
 
int *array = new int[length]; /* используем оператор new[] для выделения массива. Обратите внимание,
переменная length не обязательно должна быть константой! */
 
std::cout << "I just allocated an array of integers of length " << length << '\n';
array[0] = 7; // присваиваем элементу под номером 0 значение 7
 
delete[] array; // используем оператор delete[] для освобождения выделенной для массива памяти
array = 0; // используйте nullptr вместо 0 в C++11
```
**Иницаплизация динамического массива**:
```cpp
int *array = new int[5] { 9, 7, 5, 3, 1 }; // инициализируем через список инициализаторов
```
Важно отметить, что массива может быть инициализирован только один раз и с явным указанием размера (напр., `
int *dynamicArray1 = new int[] {1, 2, 3};` - нельзя). Чтобы изменить динамический массив надо создать новый и скопировать содержимое в него. В этому случаии лучше использовать `std::vector` (он изменяется).  Также, динамически нельзя выделить память для C-style строки. Однако, можно это сделать для `std::string`.

### 2-dimensional dynamic arrays
Они создаются на основе массива указателей. Если следующие способы их создания:
1. Если кол-во строк (rows) известно т.е. это compile-time constant, то динамически создается массив, который запоняется указателями на массивы рамера row. 
```cpp
int (*array)[7] = new int[15][7];	// 7 - num of rows (compile-time const), 15 - num of columns (run-time const)
/* начиная с С++11 можно использовать auto. В этом случаи оно само укажет, что это динамический массива, состоящий из
массивов рамера [7].
auto array = new int[15][7]; 		// То же самое, но намного проще!
```
2. Если обе рамерности 2-мерного массива - это константы типа run-time, то синтаксического сахара для них нет. Надо выделить массив (указателей на указатели) рамера num_of_columns и в цикле заполнить его выделенными массивами (вложенных указателей) размера num_of_rows:
```cpp
int **array = new int*[15]; // выделяем массив из 15 указателей типа int — это наши строки
for (int count = 0; count < 15; ++count)
    array[count] = new int[7]; // а это наши столбцы
    
/* Доступ к элементам массива выполняется как обычно */
array[8][3] = 4; // это то же самое, что и (array[8])[3] = 4;
```
Если надо, можно создать и не прямоугольные массив (например треугольный):
```cpp
int **array = new int*[15]; // выделяем массив из 15 указателей типа int — это наши строки
for (int count = 0; count < 15; ++count)
    array[count] = new int[count+1]; // а это наши столбцы
```

#### Удаление массива**    
Надо опять же циклом освободить память от каждого из вложенных массивов, а потом от основного:
```cpp
for (int count = 0; count < 15; ++count)
    delete[] array[count];	// удалили вложенный массив
delete[] array; 		// удалили основной массив
```

#### Эффективное выделение 2D массива ("сглаживание")**    
На самом деле операция выделения памяти (`new` или `alloc`) - это медленная операция, поэтому хочется свести ее к минимуму. Более того, эти оперции возвращают часть памяти из кучи в случайном месте. Отсюда возникает **проблема фрагментации** - память порезана на малые кусочки, и не получается "впихнуть" между ними новый кусок. Поэтому лучше "сглаживать" массивы по следующему принципу: 1. выделяется динамический массив указателей на указатели. 2. В его первую ячейку записывается массив такого размера, чтобы все необходимые ячейки лежали в ряд (те для m[5][4] выделяется кусок размером 5x4 = 20, чтобы последовательные ряды лежил вместе. 3. Этот массив делится на участки (напр., размера 4) адереса начала этих участков записываются в основной массив. Таким образом для m[a][b] вместо (a+1) вызовов `new`, их нужно всего 2 и нет фрагментации.
```cpp
// Вместо следующего:
int **array = new int*[15]; // выделяем массив из 15 указателей типа int — это наши строки
for (int count = 0; count < 15; ++count)
    array[count] = new int[7]; // а это наши столбцы 
 
// Делаем следующее: 
int *array = new int[105]; // двумерный массив 15x7 "сплющенный" в одномерный массив

// Ф-ция обработки индексов
int getSingleIndex(int row, int col, int numberOfColumnsInArray)
{
     return (row * numberOfColumnsInArray) + col;
}
 
// Присваиваем array[9,4] значение 3, используя наш "сплющенный" массив
array[getSingleIndex(9, 4, 5)] = 3;
```
Еще один снипет: 
```cpp
int ** create_array2d( size_t a , size_t b) 
{//создадим указатель [a][b]
	int ** m = new int *[a];	 //создаем массив указателей на указатели размера a		
	m[0] = new int[a * b];		 //в первую ячейку этого массива выделяем кусок памяти a*b	
	for ( size_t i = 1; i != a; ++i) //далее отсчитываем по b ячеек начиная с самого начала (m[0])	
		m[i] = m[i - 1] + b;	 //и записываем в последовательные ячейки 
	return m;
}
void free_array2d(int ** m, size_t a , size_t b)
{//ф-ция для удаления этого массива
	delete [] m[0];
	delete [] m;
}
```

## C-style strings
- Не рекомендуются к использованию из за опасности переполнения. Лучше использовать `std::string`.     
Это char массив. В конце строки компилятор сам добавит int 0. По нему он будет определять конец строки (напр., std::cout закончит вывод на int 0). В итоге, длинна массива будет на +1 больше. 
```cpp
char mystring[] = "string";		// лучше, чтобы компилятор сам определял длинну массива
mystring[1] = 'p';			// выведет 'spring'
```
Т.к. это по сути статический массив, то перед использованием его длинна должна быть определена. Это затрудняет использование С-style строк с оператором std::cin. В этом случаи можно объявить большой массива (напр., 255 символов) и принимать значения с помощью ф-ции `getline(str, num)`, она принимает только указанное число символов (это исключит переполнение): 
```cpp
char name[255]; 			// объявляем достаточно большой массив (для хранения 255 символов)
std::cout << "Enter your name: ";	// cin.getline() будет принимать до 254 символов в массив name 
std::cin.getline(name, 255);
std::cout << "You entered: " << name << '\n';
```
### <csting>
Библиотека полезных ф-ций для C-style sting.   
- `strcpy_s(new_str, old_str)` - копирует содержимое одной строки (old_str) в другую(new_str).
- ` strlen(my_str)` - возвращает длину строки C-style (без учёта нуль-терминатора). Причем,
	```cpp
	char name[15] = "Max"; 	// используется только 4 символа (3 буквы + нуль-терминатор)
    	std::cout << name << " has " << strlen(name) << " letters.\n";			// проверяет кол-во символов
    	std::cout << name << " has " << sizeof(name) << " characters in the array.\n" 	// проверяет размер массива
	/* выведет: Max has 3 letters. Max has 15 characters in the array. */
	```
- `strcat()` — добавляет одну строку к другой (опасно из за возможности переполнения);
- `strncat()` — добавляет одну строку к другой (с проверкой размера места назначения);
- `strcmp()` — сравнивает две строки (возвращает 0, если они равны);
- `strncmp()` — сравнивает две строки до определённого количества символов (возвращает 0, если они равны).
- `strstr(one_str, two_str)` - ищет two_str внутри one_str. 
	
### C-style string symbolic constants
```cpp
char myName[] = "John";
char *myName = "John";
//тучше всегда делать const char *myName = "John"; по причине объясенной ниже
```
Эти два объявления создают одинаковую C-style строку, но их организация разная. В 1ом случаи компилятор созадет `char array[5]` с содержимым `John\0` и записывает его write память. Во 2ом случаи, компилятор создает строковый литерал `John\0` и записывает ее в read-only память (те это просто сохраненный набор символов, который не подразумевает изменение). Несколько строковых литералов с одним и тем же содержимым могут указывать на один и тот же адрес (это значит, что если где-то в программе создается еще один строковый литерал `John\0`, то он поделит адрес с этой строкой).

- На самом деле когда мы вводим что-то вроде: `std::cout << "Something John";`. При запуске программы (те запуске .exe) в read-only памяти сохраняется литерал "Something John", а затем в теле программы ф-ция `cout` обращается по адресу, где хранится этот литерал и выводит его.

## Enumeration (enum)
**Пара общих фич**   
1. Объявление enum, struct ... не требует выделения памяти, то для них нельзя сделать предварительное объявление (прототип) при использовании из другого файла. Но есть обходной путь: чтобы использовать объявление структуры в нескольких файлах (чтобы иметь возможность создавать переменные этой структуры в нескольких файлах), надо поместить объявление структуры в заголовочный файл и #include этот файл везде, где необходимо использовать структуру. Тогда при #include объявление структуры будет просто добавлено в файл. 

### Обычный перечислитель   
Это контейнер, который хранит в себе пары {name:int}. При этом, номера (int) идут последовательно, если число явно неуказано. Так, если первый элемен не указан явно, то ему приписывается 0. Второму элементу 1 и т.д. Каждый следующий элемент по дефолту +1 от предыдущего. . По сути, в enum хранятся переменные и соответствующие им int. **enumeration (enum)** - это контейнер, **enumerator** - это элемент контейнера.
```cpp
// define a new enum named Animal
enum Animal
{
    ANIMAL_CAT = -3,	// если бы не было указано явно, то было бы 0
    ANIMAL_DOG, 	// assigned -2 (т.к. предыдущий -3)
    ANIMAL_PIG, 	// assigned -1
    ANIMAL_HORSE = 5,
    ANIMAL_GIRAFFE = 5, // shares same value as ANIMAL_HORSE
    ANIMAL_CHICKEN 	// assigned 6 (т.к. предыдущий 5)
};
```
Компилятор автоматически не конвертирует int -> enymerator. А наоборот (enymerator -> int) все ОК:
```cpp
Animal my_animal = 6; 	// will cause compiler error 
Animal my_animal = static_cast<Animal>(6); // OK. Создаст enumerator my_animal = Chicken = 6

int mypet = ANIMAL_PIG;  // OK. mypet = -1 (это будет обычный int)
```
#### Input/output
**Input/output** - воодится и выводится соответсвующий int: 
```cpp
//evaluates to integer before being passed to std::cout
std::cout << ANIMAL_HORSE; 	// вернет 5
```
Компилятор автоматически не конвертирует int -> enymerator. Т.е.:
```cpp
Animal my_animal = 6; // will cause compiler error 
Animal my_animal = static_cast<Animal>(6); // OK. Создаст enumerator my_animal = 6 (т.е. ANIMAL_CHICKEN)
```
Вывод имя перечислителя (напр., ANIMAL_CHICKEN) нельзя. Выкрутиться можно через if-else:
```cpp
void printAnimal(Animal my_animal)
{
    if (my_animal == ANIMAL_CAT)
        std::cout << "Cat";
    else if (my_animal == ANIMAL_DOG)
        std::cout << "DOG";
```
Это все из за того, что перечеслитель - это просто название переменной.

#### Scope    
Обычные пречислители в глобальной области видимости. Отсюда могут идти ошибки т.к. мы можем сравить например ANIMAL_CHICKEN (= 6) и COLOR_PINK (=6) из разных enum, и получить True (а хорошо бы, чтобы они не сравнивались и вообще не видели друг друга).

#### Память    
В среднем enumerator весит как обычный int, но это может зависеть от компилятора.

### Enum classes
Для того, чтобы enum имели локальную видимость и не были в глобальном namespace, (в С++ 11) ввели `enum class` (also called a scoped enumeration). Все то же самое, но тут нельзя сравнивать два разных enum class (но можно внутри одного). Так же для вызова надо исп. `::`:
```cpp
/* добавление "class" к enum определяет перечисление с ограниченной областью видимости, вместо стандартного "глобального" перечисления */
enum class Fruits 
{
	LEMON, // LEMON находится внутри той же области видимости, что и Fruits
	Kiwi,
	Vlad /* в случаии enum class не будет конфликта имен с Fruits из за локальной видимости. Если бы это были обычные enum, был бы конфликт из за Vlad */
};
 
enum class Colors
{
        PINK, // PINK находится внутри той же области видимости, что и Colors
        GRAY,
	Vlad  
};
 
Fruits fruit = Fruits::LEMON; // примечание: LEMON напрямую не доступен, мы должны использовать Fruits::LEMON
Colors color = Colors::PINK; // примечание: PINK напрямую не доступен, мы должны использовать Colors::PINK
	
if (fruit == color) 
/* ошибка компиляции, поскольку компилятор не знает как сравнивать разные типы: Fruits и Colors. Но можно сранивать напр. Fruits::LEMON и Fruits::Kiw; */
	std::cout << "fruit and color are equal\n";
 else
        std::cout << "fruit and color are not equal\n";
}
```

### Зачем это надо?
Для удобного ведения программы, документации и вместо магических чисел: 
```cpp
/* возможные ошибки при чтении файла и соответсвующие им флаги */
enum ParseResult
{
    SUCCESS = 0,
    ERROR_OPENING_FILE = -1,
    ERROR_PARSING_FILE = -2,
    ERROR_READING_FILE = -3
};
 
ParseResult readFileContents()
{
    if (!openFile())
        return ERROR_OPENING_FILE;
    if (!parseFile())
        return ERROR_PARSING_FILE;
    if (!readfile())
        return ERROR_READING_FILE;
 
    return SUCCESS; 	// если всё прошло успешно
}

/* Основное тело программы */
if (readFileContents() == SUCCESS)
    {
    // Всё прошло успешно -> Делаем что-нибудь
    }
else
    {
    // Выводим сообщение об ошибке
    }
```

### Short enum
Тк как под копотом emun работает на обычных int, то обычно вместимости int слишком много. Можно сделать так, чтобы enum был основан на short или char. Это позволит сохранить немного памяти, если это нужно.
```cpp
enum class Colors // это стандартный enum на int
{};
enum class Colors : short // это enum на short
{};
enum class Colors : char // это enum на char
{};
```

---


## Helpful functions
- `static_cast<type2>(variable_type1)` - переводит variable_type1 в type 2.
- `typeid()` (находится в заголовочном файле typeinfo(#include <typeinfo>) показывает тип объекта: `typeid(variable).name()`.
- `sizeof(data_type)` - вернет размер типа данных (на данной компьютере/системе/компиляторе).   
Если число большего размера, чем переменная, то будет **переполнение**: отбросятся первые невмещающиеся биты: 
```cpp
int main()
{
    unsigned short x = 65535; // наибольшее значение, которое может хранить 16-битная unsigned переменная
    std::cout << "x was: " << x <<l; 
    x = x + 1; // 65536 - это число больше максимально допустимого числа из диапазона допустимых значений. Следовательно, произойдёт переполнение, так как переменнная x не может хранить 17 бит
    std::cout << ", x is now: " << x << std::endl;
}
```
Выйдет: `x was: 65535, x is now: 0` т.к. 65 535 в двоичной системе это 1111 1111 1111 1111, 65 536 представлено в двоичной как 1 0000 0000 0000 0000 (занимает 17 бит!). Обрезается 1й бит (т.е. 1), остается 0000 0000 0000 0000 т.е. 0. *Если из `unsigned short x = 0` вычесть 1, то будет 65 535* (т.е. на границе счетчик (range: 0 - 65 535) прокручивается в обе стороны).
