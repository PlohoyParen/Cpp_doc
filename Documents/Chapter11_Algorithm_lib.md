# Chapter 11 Algorithm lib
## A few things algorithm relies on 
`RamdomAccessIterator` - итератор, который может обращаться напрямую у любому элементу контейнера. Таким итератором обладают контейнеры, которые лежать в памяти последовательно, те нам не надо знать где лежит предудщий элемент, чтобы узнать последующий.     
`bidirectional_iterator` - Bidirectional iterators are iterators that can be used to access the sequence of elements in a range in both directions (towards the end and towards the beginning). `RamdomAccessIterator > bidirectional_iterator`.    
`forward_iterator` - Forward iterators are iterators that can be used to access the sequence of elements in a range in the direction that goes from its beginning towards its end. `RamdomAccessIterator > bidirectional_iterator > forward_iterator`.    

### Comparasing functors
Для некоторых алгоритмов используются функторы, найти которые можно в `<functional>` и написать свои.   
#### functional lib
Для некоторых алгоритмов используются функторы, найти которые можно в `<functional>`.    
Это templates для операций сравнения:
- `equal_to` и `not_equal_to`
- `greater` и `less` 
- `greate_equal` и `less_equal` 
Пример:
```cpp
...
#include<functional>
std::vector<int> vec = { 25,6,7,9,32,6,3,2,6,72,0 };
std::sort(vec.begin(), vec.end(), std::greater<int>{});
```

#### Свой функтор сравнения 
Иногда полезно создать свой функтор, наприрмер, если сранение объектов класса может поросходить по нескольким принципам. Тогда наиболее очевыдный (например, id объекта) будет использовать операторы сравнения, а альтенативные способы будут реализованы через функторы:
```cpp
class Dude
{
private:
    int id; 
    int a;
    int b;
public:
    Dude(int id, int a, int b) : id(id), a(a), b(b) {}
    bool operator<(const Dude& rhs) const   
    {   return id <rhs.id;  }               //основной метод сравнения, будет полагаться на функторы из <funcltional>
    
    class ALess
    {
        bool operator()(const Dude& lhs, const Dude& rhs)   
        {   return lhs.a < rhs.b;   }       //альтернативный метод сравнения, будет вызываться для сравнения через a
    }
};
```

## <Algorithm>
### std::sort              
1. `sort(begin, end)` - сортировка контейнера c `RamdomAccessIterator`(string, array etc; у контейнеров с bidirectional/forward iterator обычно есть собственные методы для сортировки). The algorithm used by sort() is *IntroSort*. Introsort being a hybrid sorting algorithm uses three sorting algorithm to minimise the running time, *Quicksort*, *Heapsort* and *Insertion Sort*, taking the fastest fot the case. Comlexity: O(N*log(N)).          
    ```cpp
    #include <algorithm>
    sort(begin(nums), end(nums)) //принимет итератор на начало и на конец
    ```

2. Операция сравенения задается функтором. По дефолту это `less`. Можно явно задать нужный функтор сравнения:
    ```cpp
    ...
    #include <functional>
    std::vector<int> vec = { 25,6,7,9,32,6,3,2,6,72,0 };
    std::sort(vec.begin(), vec.end(), std::greater<int>{});
    ```
    Для собственных типов нужно определить оператор сравнения, и тогда алгоритм сможет отсортировать контейнер содержащий объектры класса:
    ```cpp
    class Dude
    {
    private:
        int id; 
        int a;
        int b;
    public:
        Dude(int id, int a, int b) : id(id), a(a), b(b) {}
        bool operator<(const Dude& rhs) const   
        {   return id <rhs.id;  }               //основной метод сравнения, будет полагаться на функторы из <funcltional>

        class ALess
        {
            bool operator()(const Dude& lhs, const Dude& rhs)   
            {   return lhs.a < rhs.b;   }       //альтернативный метод сравнения, будет вызываться для сравнения через a
        }
    };

    std::vector<Dude> vec = { {25,6,7},{9,32,6},{3,2,6}};
    std::sort(vec.begin(), vec.end());  //needs operator <
    //std::sort(vec.begin(), vec.end(), std::greater<Dude>{}); // would need operator >
    std::sort(vec.begin(), vec.end(), Dude::Aless{});          //Создаем экземпляр функтора
    ```
    
3. Требоания к контейнеру:
    - RandomIt must meet the requirements of `ValueSwappable` and `LegacyRandomAccessIterator`.
    - The type of dereferenced RandomIt must meet the requirements of `MoveAssignable` and `MoveConstructible`.
    - Compare must meet the requirements of `Compare`.
 
### std::count 
`count(begin, end, what)` - считает *what* в любом контейнере (включая list, forward_list etc):
    ```cpp
    #include <algorithm>
    vector<int> nums= {1,2,3, 5, 6,43, 5};
    int quantity = count(begin(nums), end(nums), 5);
    cout << qunantity;         //will return 2
    ```

3. `swap(a, b)` - меняем местами значения переменных a и b (напр., a=1, b=2 -> a=2, b=1)

4. `std::set_difference(s1.begin(), s1.end(), s2.begin(), s2.end(), result_inserter)`- принимает два контейнера (не обязвательно set) в форме: begin(), end() и возвращает интератор с их разницей. Контейнеры должны быть **отсортированы** (set уже отсортирован, а наример vector надо сортировать).    
```cpp
#include <algorithm>
#include <set>
#include <iterator>
// ...
std::set<int> s1, s2;
// Fill in s1 and s2 with values
std::set<int> result;
std::set_difference(s1.begin(), s1.end(), s2.begin(), s2.end(),
    std::inserter(result, result.end()));
