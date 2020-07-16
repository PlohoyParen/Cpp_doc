# Chapter16: Virtual functions
**Виртуальная функция** — это особый тип функции, которая, при её вызове, вызывает «наиболее» дочерний метод, который существует между родительским и дочерними классами. Для этого перед переопердяемой ф-цией ставится ключевое слово `virtual`.
## Полиморфизм (и постановка задачи)
**Полиморфизм** -  возможность объектов с одинаковой спецификацией иметь различную реализацию: "Один интерфейс, множество реализаций". Виртуальные ф-ции - это ключевой аспект реализации полиморфных объектов.    
Например, у нас есть класс Animal, который мы хотим использовать как основу для более специфических классов, например, Cat, Dog etc., 
```cpp
class Animal
{
protected:
    std::string m_name;

    /* Мы делаем этот конструктор protected так как не хотим,  чтобы пользователи создавали 
    объекты класса Animal напрямую, но хотим, чтобы у дочерних классов доступ был открыт */
    Animal(std::string name)
        : m_name(name)
    {
    }
public:
    std::string getName() { return m_name; }
    const char* speak() { return "???"; }
};
```
Теперь создадим дочерние классы на его основе, 
```cpp
class Cat: public Animal
{
public:
    Cat(std::string name)
        : Animal(name)
    {
    }
 
    const char* speak() { return "Meow"; } //переопределили ф-цию speak для класса Cat
};
 
class Dog: public Animal
{
public:
    Dog(std::string name)
        : Animal(name)
    {
    }
 
    const char* speak() { return "Woof"; }  //переопределили ф-цию speak для класса Dog
};
```
### Пример 1
Теперь, если мы захотим создать универсальный интерфейс работы с классами Cat и Dog, то у нас возникнет проблема. Например, если я хочу создать независимую ф-цию, вызвающую `speak()` для обоих классов, то мне придется сделать 2 перегрузки принимаюшие `Cat& cat` и `Dog& dog`. Есть решение по-лучше: я могу взять их общую точку отсчета - родительский класс Animal :`Animal& animal`,
```cpp
/*** Вместо этого ***/
void report(Cat &cat)
{
    std::cout << cat.getName() << " says " << cat.speak() << '\n';
}
 
void report(Dog &dog)
{
    std::cout << dog.getName() << " says " << dog.speak() << '\n';
}

/*** Сделать это ***/
void report(Animal &rAnimal)
{
    std::cout << rAnimal.getName() << " says " << rAnimal.speak() << '\n';
}
```
Проблема в том, что при вызове `report(cat)` будет сделан "срез" родительской части (тк ссылка идеть только на родительскую часть), а значит перегрузка ф-ции `speak()` не будет учтена и бедт вызвана ее родительская форма.     
Тут на помощь приходят виртуальные ф-ции. В этом случаии, если мы сделаем `virtual const char* speak() { return "???"; }` в родительском классе, то компилятор, увидя, что ф-ция виртуальная, будет искать наиболее дочернюю перегрузку этой ф-ции (те ф-ции дочерних классов Cat и Dog).     
### Пример 2 
Хотим создать массив, куда соберем разных животных. Тк Cat и Dog - это разные массивы, то с этим будет проблема. Можно аналогичено создать массив класса Animal, а методы, которые мы хотим использовать для их элементов сделать вирутальными,
```cpp
Cat matros("Matros"), ivan("Ivan"), martun("Martun");
Dog barsik("Barsik"), tolik("Tolik"), tyzik("Tyzik");
 
// Создаём массив указателей на наши объекты Cat и Dog
Animal *animals[] = { &matros, &barsik, &ivan, &tolik, &martun, &tyzik};
for (int iii=0; iii < 6; ++iii)
    std::cout << animals[iii]->getName() << " says " << animals[iii]->speak() << '\n'
```

## Особенности использования виртуальных ф-ций
### Пример цепочки наследования 
```cpp
class A
{
public:
    virtual const char* getName() { return "A"; }
};
 
class B: public A
{
public:
    virtual const char* getName() { return "B"; }
};
 
class C: public B
{
public:
    virtual const char* getName() { return "C"; }
};
 
class D: public C
{
public:
    virtual const char* getName() { return "D"; }
};
 
int main()
{
    C c;
    A &rParent = c;
    std::cout << "rParent is a " << rParent.getName() << '\n';
 
    return 0;
}

/***** Будет выведенно *****
rParent is a C
***************************/
```
1. 
