# ChapterX: CUDA-C/C++
## Основы
В целом GPU нужны, чтобы быстро параллельно обрабатывать простые вычисления. Такие вычисления встречаются, например, при рендеринге. Мы рендерим фрейм сотни раз в секундку (60 frame это хорошо для игр, например). Каждый раз компьютер должен просчитать как и куда рансформируются точки (vertices) из которых состоят объекты в графике. Это просте вычисления, но их очень много. Так GPU делает простые вычисления параллельно. Сравнивая CPU и GPU получим у меня на ноуте (intel core i7 и GeForce960M) получим, что у CPU 4 гигогерцовых ядра, а у GPU 640 мегагерцовых ядер (для ноута mobile версия, для обычной GeForce960 - 1024 ядер). Отсюда, **GPU хорош для параллельных алгоритмов, а CPU для последовательных**.     

CUDA - это специальное API для видеокарт NVIDIA. Хотя синтаксис аналогичен C/C++ с небольшими добавлениями, под капотом он реализован на PTX языке (Parallel Thread Execution (PTX, or NVPTX) - это псевдо-ассемблер разработанный NVIDIA), который потом переводтся в в бинарный код для GPU. Надо понимать, что это CUDA - это не библиотека для С/С++ - это считай что отдельный язык, который называется CUDA-C/C++. Для его реализации нужен специальный компилятор NVCC (не входит в стандартные пакеты IDE), а файлы с кодом имеют расширение `.CU` (в VS можно выставить опцию, что .CU файлы автоматически ассоциировались с NVCC компилятором).     

### Термины
**Host** - это CPU. Host is in control of the system RAM, disks and other devises.       
**Device** - это GPU. It has its own RAM, and it can control only this RAM. It doesn't have access to system RAM, disks and other deivses.       
**Kernel** - так называют ф-ции написаные на CUDA языке, которая выпоняется на GPU. Каждый kernel может выполняться параллельно на 100- 1000 threads. На kernel накладываются следующие ограничения:     
  1. No recursive kernels
  2. kernels always return void
  3. They can't use system memory (?)
  4. Kernel can't have variable number of arguments
  
**Function qualifiers** - это приставки, которые пишутся перед ф-цией, чтобы компилятор понял, что это kernel. There are 3 function qualifiers (используется два черточки `__`, а не одна `_`):    
  1. `__global__` - called by host, runs on device (те вызывается, например, из main()).
  2. `__device__` - called by device, runs by device (вспомогательные ф-ции, которые вызываются из других kernel).
  3. `__host__` - normal host function (те device ее вообще не трогает. Все делается на CPU). На самом деле NVCC компилятор передает такие ф-ции напрямую обычному компилятору C, чтобы тот сам разбирался со своими CPU ф-циями.      
  
### System (RAM) and graphic card memory  
CPU имеет доступ к RAM (на ней он и считает, стэкает и хипает), но не имеет доступа к памяти видеократы. У GPU есть доступ к память видеокарты (напримре, у GeForce 960m - это 2GB), но не имеет доступа к RAM. Поэтому возникает проблема: основная часть программы (Host) распоряжается RAM, а kernel распоряжаются только памятью видеократы. Во время программы нужно передавать данные из RAM на память видеократы (и обратно). Следующие ф-ции нужны для этого:
- `cudaMalloc(int **devPrt, sizeof(int))` - выделяет память на device (те на видеокарте). Первый параметр - указатель на указатель на нужного типа (тут int), второй параметр - это размер выделяемой памяти (тут на 1 int).
- `cudaFree(int* devPrt)` - высвобождает память на device соответвующую данному указателю.
- ` cudaMemcpy(void* dest, void* src, sizeof(int), enum direction)` - ф-ция копирующая содержимое указателя src в указатель dest. Нужна для передачи данных с Host на device (и обратно). При этом надо указать направление передачи(enum direction): `cudaMemcpyHostToDevice` и `cudaMemcpyDeviceToHost`. На самом деле есть еще `cudaMemcpyHostToHost` и `cudaMemcpyDeviceToDevice`, но не оч понятно зачем.
  
### Пример и общий workflow
Ниже программа, которая складывает 2 числа на GPU (очень глупо и CPU, конечно, сделает это лучше).  
```cpp
//#include "cuda_runtime.h"
//#include "device_launch_parameters.h"

#include <iostream>
#include <cuda.h>

__global__ void AddTwo(int* a, int* b)
{
    *a += *b;
}

int main()
{
//1
    int a = 5;
    int b = 10;
    int* d_a, * d_b;
//2
    cudaMalloc(&d_a, sizeof(int));
    cudaMalloc(&d_b, sizeof(int));
//3
    cudaMemcpy(d_a, &a, sizeof(int), cudaMemcpyHostToDevice);
    cudaMemcpy(d_b, &b, sizeof(int), cudaMemcpyHostToDevice);
//4
    AddTwo <<<1, 1 >> > (d_a, d_b);
//5    
    cudaMemcpy(&a, d_a, sizeof(int), cudaMemcpyDeviceToHost);
//6    
    cudaFree(d_a);
    cudaFree(d_b);
    
    std::cout << a << std::endl;
    return 0;
}
```
Общий workflow следующий:
1. На Host создаются нужные нам объекты.
2. На Device выделятеся память достаточная для этих объектов.
3. Эти объекты копируются с Host на выделенный блок памяти на Device.
4. Вызывается kernel (тк он ничего не возвращает, то все действия производятся in-place, те мы передаем все что хотим поменять через ссылки и указатели).
5. Копируем измененные объекты с Device обратно на Host.
6. Высвобождаем память на Device.


- Библиотеки
  ```cpp
  //#include "cuda_runtime.h"
  //#include "device_launch_parameters.h"
  
  #include <cuda.h>
  ```
  Нужно добавить либо первые 2, либо последнюю (cuda.h). Последняя старая бибилиотка (и она же была в туториалах). Если же добавить первые 2, то появиться подсветка индекса в VS и он перестанет ругаться но CUDA спецефический код.
- Kernel. Вызов kernel имеет следующий ситаксис: `AddTwo <<<1, 1 >> > (d_a, d_b)`. Числа в `<<<n, m>>>`: n-number of thread blocks, m-number of threads in one block. Те тут мы вызываем kernel с 1 блоком и 1 потоком в блоке.









### Error "Display Driver has Stopped Working and has Recovered"
Проблема связана с тем, что widows автоматически перезапускает драйвер GPU, если видеокарта не отвечает более 2сек. Те если мы выполняем программу на GPU дольше 2сек, то widows будет думать, что что-то посшло не так и перезагрузит драйвер. Нужно поменять найстройки регистров. Подробнее [тут](https://www.youtube.com/watch?v=8NtHDkUoN98&list=PLKK11Ligqititws0ZOoGk3SW-TZCar4dK&index=3). Обычно программа просто дропает выполнение kernel, а CPU продолжает работу доводя программу до завершения, так что проблема может быть не очевидна.
