# Chapter19: Error handling
## Motivation

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
1. *Terminate a programm* due to the error
2. *Handle the error*
