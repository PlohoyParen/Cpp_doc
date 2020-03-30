# Chapter14 Operator overload
Главное, что надо знать о перегрузке операторов - это то, что оператор можно перегрузить только, если хотя бы один из операндов - пользовательский тип. Например, можно перегрузить class Smth + int, но нельзя перегрузить int + int. Так классы это практически (?) едиственный сбособ создания пользовательских типов, то перегрузка в основном нужна для классов. Отсюда происходят 2 способа перегрузки:
1. Через внешние (обычные) ф-ции (или дружественные, если нужен доступ к скрытым членам)
3. Через ф-ции класса

В то же время сами операторы можно разделить на два типа:
1. Унарные (один опреанд)
2. Бинарные (два операнда)

## Перегрузка через через обычне ф-ции
В этом случаии используется ключевое слово и символ оператора: `operator+`. В качестве аргументов принимаются как левая так и правая сторона: `operator+(const class_one& one, const class_two& two)`. Также, тк доступ к членам не прямой, то **возврат будет по-значению**. Например,
```cpp
class Dollars
{
private:
	int m_dollars;
public:
	Dollars(int dollars) { m_dollars = dollars; }
	int getDollars() const { return m_dollars; }    //создали геттер для доступа к членам
};
// Примечание: Эта функция не является ни методом класса, ни дружественной классу Dollars!
Dollars operator+(const Dollars &d1, const Dollars &d2)
{
	// Используем конструктор Dollars и operator+(int, int)
  // Здесь нам не нужен прямой доступ к закрытым членам класса Dollars
	return Dollars(d1.getDollars() + d2.getDollars());
}
 
int main()
{
	Dollars dollars1(7);
	Dollars dollars2(9);
	Dollars dollarsSum = dollars1 + dollars2;
 
	return 0;
}
```
### Перегрузка через дружественные ф-ции
Если требуется доступ скрытым членам ф-ции (не через геттер), то можно использовать `friend`:
```cpp
class Values
{
private:
	int a;
	int b;
 
public:
	Values() = default; 
   
  friend Values operator+(const Values &v1, const Values &v2);
	friend Values operator+(const Values &v, int value);
	friend Values operator+(int value, const Values &v);
};
 
Values operator+(const Values &v1, const Values &v2)
{
  int aa = v1.a + v2.a;
  int bb = v1.b + v2.b;
	return Values(aa, bb);
}
 
Values operator+(const Values &v, int value)
{
	int aa = v1.a + value;
  int bb = v1.b + value;
	return Values(aa, bb);
}
 
Values operator+(int value, const Values &v)
{
	// Вызываем operator+(Values, int)
	return v + value;
}
 
int main()
{
	Values v1(11, 14);
	Values v2(7, 10);
	Values v3(4, 13);
  // все будет работать ок даже в такой последовательности операций
	Values vFinal = v1 + v2 + 6 + 9 + v3 + 17;
 
	return 0;
}
```
Главное **удобство использования перегрузки через обычные/дружественные ф-ции - это симетрия**: те можно легко менять местами левый и правый операнды: `operator+(class Smth one, int two)` и `operator+(int two, class Smth one)`.
### Когда лучше использовать
- Для перегрузки бинарных операторов, которые не изменяют левый операнд (например, operator+).
- Важно, что **возврат по-значению**, а это значит, что цепочки операторов невозможны (точнее кодый разбудет создаваться и уничтожаться новый объект).

## Перегрузка через метод класса
В этом случаи нужно указывать только **левый операнд**, так как правый это указатель на данный экземпляр (`*this`): видим `operator+(int a)`, читаем `operator+(*this, int a)`. Например,
```cpp
class Dollars
{
private:
    int m_dollars;
public:
    Dollars(int dollars) { m_dollars = dollars; }
    // Выполняем Dollars + int
    Dollars operator+(int value);
};
// Примечание: Эта функция является методом класса!
// Вместо параметра dollars в перегрузке через дружественную функцию здесь неявный параметр, на который указывает указатель *this
Dollars Dollars::operator+(int value)
{
    return Dollars(m_dollars + value);
}
 
int main()
{
	Dollars dollars1(7);
	Dollars dollars2 = dollars1 + 3; 
	return 0;
```
Так как правый операнд всегда будет `*this`, то перегрузка симметричных операторов (например, class+int и int+class) будет невозможна.
### Как перегрузить симетричный оператор не теряя инкапсуляции 
**Использовать `friend` внутри класса** 
```cpp
class Integral
{
public:
  Integral() = default;
  Integral(const int value) : value{value}{};

  friend auto operator+(const int left, const Integral& right) -> Integral
  {
    return Integral{left + right.value};
  }

private:
  int value{};
};
```
**Делать явное преобразование нужного типа в класс**    
Вместо int+class,  int->class и class+class:    
```cpp
class Integral
{
public:
  Integral() = default;
  Integral(const int value) : value{value}{};

  auto operator+(const Integral& right) const -> Integral
  {
    return Integral{value + right.value};
  }

private:
  int value{};
};

auto x = Integral{12};
auto result1 = x + 2; // works
auto result2 = 2 + x; // also works
```
### Когда лучше использовать
- Для операторов присваивания (=), индекса ([]), вызова функции (()) или выбора члена (->).
- Для унарных операторов (например, ! или + и -).
- Для перегрузки бинарных операторов, которые изменяют левый операнд (например, operator+=).

### Создание цепочек операторов (operator chaining)
В этом случаи возврат значения должен быть по ссылке, а в ф-цию передаваться параметр (`*this`), те это надо делать через методы класса. Например,
```cpp
//Обычное умножение class * float, объект НЕ изменяется -> возвращаем по значению
Vec2 Vec2::operator*( float rhs ) const
{
	return Vec2( x * rhs,y * rhs );
}
// Умножение class *= float, объект изменяется -> возвращаем по ссылке
Vec2& Vec2::operator*=( float rhs )
{
	return *this = *this * rhs;
}
```

### Перегрузка унарных операторов
Никакой магии тут нет, все как для перегрузки других операторов. Возврат по-значению, тк используя операторы -,+,! мы не хотим менять наш объект, а хотим получить навый с нужным свойством.
```cpp
class Something
{
private:
	double m_a, m_b, m_c;
public:
	Something(double a = 0.0, double b = 0.0, double c = 0.0) 
		:
		m_a(a), 
		m_b(b), 
		m_c(c)
	{}
 	// Конвертируем объект класса Something в отрицательный 
	Something operator- () const
	{
		return Something(-m_a, -m_b, -m_c);
	}
 
	// Возвращаем true, если используются значения по умолчанию, в противном случае - false
	bool operator! () const
	{
		return (m_a == 0.0 && m_b == 0.0 && m_c == 0.0);
	}
}
```

## Перегрузка операторов сравнения
Тк операторы сравненения (`!=`, `==`, `<`, `<=` etc) - это бинарные оператор, то их имеет смысл реализовывать через обычные/дружественные ф-ции: если сраниваются объекты разных типов и нужна симмерия. Однако, если идет сравнение между объектами одного класса, то лучше использовать метод класса.
```cpp
class Dollars
{
private:
    int m_dollars;
public:
    Dollars(int dollars) { m_dollars = dollars; }
    //Опредилим эти операторы через дружественные ф-ции
    friend bool operator> (const Dollars &d1, const Dollars &d2);
    friend bool operator>= (const Dollars &d1, const Dollars &d2);
    //А этм через методы классов. Тк в обоих случаях сравниваются класса, то не важно, что использовать
    bool operator< (const Dollars &d2);
    bool operator<= (const Dollars &d2);
};
 
bool operator> (const Dollars &d1, const Dollars &d2)
{
    return d1.m_dollars > d2.m_dollars;
}
 
bool operator>= (const Dollars &d1, const Dollars &d2)
{
    return d1.m_dollars >= d2.m_dollars;
}
//Не забыть Dollars:: тк это метод класса, определенный вне класса
bool Dollars::operator< (const Dollars &d2)
{
    return m_dollars < d2.m_dollars;
}
 
bool Dollars::operator<= (const Dollars &d2)
{
    return m_dollars <= d2.m_dollars;
}
```

## Перегрузка инкремента и декремента
На самом деле, операции `++a` (версия **префикс**) и `a++` (**постфикс**) отличаются принципиально: `++a` возвращает (a+1), в то время как `a++` возвращает a и лишь потом прибавляет 1. Поэтому реализации у них тоже разные.
```cpp

class Number
{
private:
    int m_number;
public:
    Number(int number=0)
        : m_number(number)
    {
    }
 
    Number& operator++(); // версия префикс
 
    Number operator++(int); // версия постфикс
};
 
Number& Number::operator++()
{
    // Если значением переменной m_number является 8, то выполняем сброс на 0
    if (m_number == 8)
        m_number = 0;
    // В противном случае просто увеличиваем m_number на единицу
    else
        ++m_number;
 
    return *this;
}
 
Number Number::operator++(int)
{
    // Создаём временный объект класса Number с текущим значением переменной m_number
    Number temp(m_number);
 
    // Используем оператор инкремента версии префикс для реализации перегрузки оператора инкремента версии постфикс
    ++(*this); // реализация перегрузки
 
    // Возвращаем временный объект
    return temp;
}
```
1. `++a` работает как от нее и ожидаешь: принимает скрытый указатель (`*this`), делает инкремент и возвращает ссылку на `*this`.
2. `a++` работает сложнее. 
	- Во-первых компилятр должен различить префикс и постфикс версии оператора: для этого в аргументы ставится "заглушка" просто тип данных int (`operator++(int)`), это не несет в себе никакого другого смысла кроме как сказать компилятору, что это постфикс версия.
	- Во-вторрых, мы возвращаем по значению новый обыъект-копию, который был создан до инкремента. Тк return закончит выполнение ф-ции, а мы должны при этом еще успеть сделать инкремент, то это удобный способ. Мы создали копию, сделали инкремент к оригиналу (переданному по сслыке), и вернули копию (по значению). В это же время оригинал уже был изменен.

## Перегрузка операторов ввода и вывода


