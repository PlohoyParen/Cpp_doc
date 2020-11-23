# Chapter19: Error handling
## 1. Motivation

There are two types of errors:
1. **Syntax errors** - wrong C++ syntax. Compiler will find it.      
    Например:
    ```
    f 5 > 6 then write "not equal";
    ```
    Вместо:
    ```cpp
	if (5 > 6)
	    std::cout << "not equal";
    ```
2. **Semantic errors** - code that is correct from syntax perspective (compiler won't pick it up), but which produces wrong/unexpected behaviour. There are few types:
    1. **Logic errors** - errors occur in logic statements
        ```cpp
        	if (x >= 5)
	            std::cout << "x is greater than 5";
        ```
    2. **Violated assumption** - programmer assumes some behavior from user/code/compiler which can be violated:
        ```cpp
        	char strHello[] = "Hello, world!";
        	std::cout << "Enter an index: ";
        	 
        	int nIndex;
        	std::cin >> nIndex; //user can give any input, not nessesarely `unsigned int`
        	 
        	std::cout << "Letter #" << nIndex << " is " << strHello[nIndex] << std::endl;
        ```

**Defensive programming** - programmer predicts where error could appear. You can:
1. Terminate a programm due to the error
2. Handle the error 


## 2. std::assert(), std::exit() and std::cerr()
### std::assert() and std::static_assert()
`std::assert(logic_condition)` is a preprocessor macro that evaluates a conditional expression (logic) at runtime. If the conditional expression is true, the assert statement does nothing. If the conditional expression evaluates to false, an error message is displayed and the program is terminated. 
```cpp
double calculateTimeUntilObjectHitsGround(double initialHeight, double gravity)
{
  assert(gravity > 0.0); // The object won't reach the ground unless there is positive gravity.
 
  if (initialHeight <= 0.0)
  {
    return 0.0;		// The object is already on the ground. Or buried.
  }
  return std::sqrt((2.0 * initialHeight) / gravity);
}
```
To make it more discriptive you may write assert with `&&` "error messsage". Since not empty string is always True, than `condition && message` will be the same as the condition. 
```cpp
assert(found && "Car could not be found in database");
```

`std::static_assert<logic_condition, message>` is the same thing but it is checked by compiler at compile time. 
```cpp
static_assert(sizeof(long) == 8, "long must be 8 bytes");
static_assert(sizeof(int) == 4, "int must be 4 bytes");
 
int main()
{
	return 0;
} 
```

### std::cerr
It is a stream (just like `std::cout`), so it stops the program and returns the stream to terminal/file: 
```cpp
void printString(const char *cstring)
{
    // Only print if cstring is non-null
    if (cstring)
        std::cout << cstring;
    else
        std::cerr << "function printString() received a null parameter";
}
```

### std::exit()
This command just terminates the program and return the indicated code the OS (1 is normal termination, any other number is an error):
```cpp
#include <cstdlib> // for std::exit()
#include <array>
 
int getArrayValue(const std::array<int, 10> &array, int index)
{
    // use if statement to detect violated assumption
    if (index < 0 || index >= static_cast<int>(array.size()))
       std::exit(2); // terminate program and return error number 2 to OS
 
    return array[index];
}
```
Termination means that the program is stopped and all the data is lost (unless it was handled before `exit` call).

## 3. Exceptions
There are 3 keywords:
- **throw** used to throw an exception. It could be any type:
	```cpp
	throw -1; // throw a literal integer value
	throw ENUM_INVALID_INDEX; // throw an enum value
	throw "Can not take square root of negative number"; // throw a literal C-style (const char*) string
	throw dX; // throw a double variable that was previously defined
	throw MyException("Fatal Error"); // Throw an object of class MyException
	```
- **try** - *observer block*. The block where the program looks for exceptions.
- **catch(type)** - *handler block*. The block where the problem is handled before program proceeds. It is specific to the type. So, 
	- `catch(int x)` - will catch only `throw` with int value. `
	- `catch(const std::string& str)` - will catch only `throw` with string. Чтобы передача в блок не происходила значению (те копированием), то сложные типа надо проверять/передавать по const reference.
	- `catch(const Dude& my_dude)` - только для `throw` передающего объект класса Dude.
```cpp
#include <iostream>
#include <string>
 
int main()
{
    try	
    {	// Statements that may throw exceptions you want to handle go here
        throw -1; // here's a trivial example
    }
    catch (int x)
    {   // Any exceptions of type int thrown within the above try block get sent here
        std::cerr << "We caught an int exception with value: " << x << '\n';
    }
    catch (double) // no variable name since we don't use the exception itself in the catch block below
    {
        // Any exceptions of type double thrown within the above try block get sent here
        std::cerr << "We caught an exception of type double" << '\n';
    }
    catch (const std::string &str) // catch classes by const reference
    {
        // Any exceptions of type std::string thrown within the above try block get sent here
        std::cerr << "We caught an exception of type std::string" << '\n';
    }
 
    std::cout << "Continuing on our merry way\n";
 
    return 0;
}
```
