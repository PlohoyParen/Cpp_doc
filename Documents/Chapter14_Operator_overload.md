
--\-111111111111111111111111111111111# Chapter14 Operator overload
Главное, что надо знать о перегрузке операторов - это то, что оператор можно перегрузить только, если хотя бы один из операндов - пользовательский тип. Например, можно перегрузить class Smth + int, но нельзя перегрузить int + int. Так классы это практически (?) едиственный сбособ создания пользовательских типов, то перегрузка в основном нужна для классов. Отсюда происходят 2 способа перегрузки:
1. Через внешние (обычные) ф-ции (или дружественные, если нужен доступ к скрытым членам)
3. Через ф-ции класса

В то же время сами операторы можно разделить на два типа:
1. Унарные (один опреанд)
2. Бинарные (два операнда)

## Перегрузка через через обычне ф-ции
В этом случаии используется ключевое слово и символ оператора: `operator+`. В качестве аргументов принимаются как левая так и правая сторона: `operator+(const class_one& one, const class_two& two)`. Например,
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

## Перегрузка через метод класса
