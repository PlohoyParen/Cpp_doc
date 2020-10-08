# Chapter 11 Algorithm lib
## A few terms
`RamdomAccessIterator` - итератор, который может обращаться напрямую у любому элементу контейнера. Таким итератором обладают контейнеры, которые лежать в памяти последовательно, те нам не надо знать где лежит предудщий элемент, чтобы узнать последующий.     
`bidirectional_iterator` - Bidirectional iterators are iterators that can be used to access the sequence of elements in a range in both directions (towards the end and towards the beginning). `RamdomAccessIterator > bidirectional_iterator`.    
`forward_iterator` - Forward iterators are iterators that can be used to access the sequence of elements in a range in the direction that goes from its beginning towards its end. `RamdomAccessIterator > bidirectional_iterator > forward_iterator`.    


## Ф-ции
### std::sort 
(Reference)[https://en.cppreference.com/w/cpp/algorithm/sort]                 
`sort(begin, end)` - сортировка контейнера c `RamdomAccessIterator`(string, array etc; у контейнеров с bidirectional/forward iterator обычно есть собственные методы для сортировки). The algorithm used by sort() is *IntroSort*. Introsort being a hybrid sorting algorithm uses three sorting algorithm to minimise the running time, *Quicksort*, *Heapsort* and *Insertion Sort*, taking the fastest fot the case. Comlexity: O(N*log(N)).
    ```cpp
    #include <algorithm>
    sort(begin(nums), end(nums)) //принимет итератор на начало и на конец
    ```
    Требоания к контейнеру:
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
