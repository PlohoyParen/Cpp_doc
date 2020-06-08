# Chapter X SFML
**SFML** (с англ. «Simple and Fast Multimedia Library» = «Простая и быстрая мультимедийная библиотека») - свободная кроссплатформенная мультимедийная библиотека, написанная на C++, но также доступна и для C, C#, .Net, D, Java, Python, Ruby, OCaml, Go и Rust. Представляет собой объектно-ориентированный аналог SDL. На ней можно писать 2D графические приложения.
## Создание проекта и добавление зависемостей
Подробнее [тут](https://ravesli.com/graficheskaya-biblioteka-sfml-vstuplenie-i-ustanovka/). Последовательность действий:
1. подключить каталог заголовочных и исходных файлов SFML (/include);
2. подключить каталог библиотечных файлов SFML (/lib);
3. подключить библиотечные файлы SFML в качестве дополнительных зависимостей;
4. скопировать .dll файлы в проект.

## Скилет программы
```cpp
#include <SFML/Graphics.hpp>

using namespace sf;

int main()
{
	// Объект, который, собственно, является главным окном приложения
	RenderWindow window(VideoMode(1200, 600), "SFML Works!");

	// Главный цикл приложения. Выполняется, пока открыто окно
	while (window.isOpen())
	{
		// Обрабатываем очередь событий в цикле
		Event event;
		while (window.pollEvent(event))
		{
			// Пользователь нажал на «крестик» и хочет закрыть окно?
			if (event.type == Event::Closed)
				// Тогда закрываем его
				window.close();
		}
		window.clear(Color(250, 220, 100, 0));
		// Отрисовка окна	
		window.display();
	}

	return 0;
}
```
- `RenderWindow window(VideoMode(1200, 600), "SFML Works!");` - окно выводимое на экран - это объект класса RenderWindow. Тут `VideoMode(1200, 600)` - класс задающий размеры окна, `"SFML Works!"` - название окна. Все взаимодействия в с окном производятся над объектом  window. Например, `window.display();` - вызывает отрисовку окна. `window.close();` - закрытие окна.
- `window.clear(Color(250, 220, 100, 0));` - заливка окна цветом заданным объектом Color. `(Color(R, G, B, alpha));` - цвет задается через RGB и aplha (уровень прозрачности).
- `while (window.isOpen()){...}` - главный loop. Тут обрабатываются все действия между последовательными отрисовками. 
- `while (window.pollEvent(event))` - loop обработки событий. Он последовательно "вытягивает" event из очериди событий. `pollEvent(event)` - "вытягивание" события. `event.type == Event::Closed` - если нажали на крестик, то это событие ::Closed.
