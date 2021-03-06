Сборка проекта на С++ в GNU/Linux
#################################

:date: 2018-09-24 09:00
:summary: Сборка проекта на С++ в GNU/Linux.

.. default-role:: code

Введение
========

Язык С++ является *компилируемым*, то есть трансляция кода с языка высокого уровня на инструкции машинного кода происходит не в момент выполнения, а заранее — в процессе изготовления так называемого *исполняемого файла* (в ОС Windows такие файлы имеют расширение `.exe`, а в ОС GNU/Linux чаще всего не имеют расширения).

hello.cpp
---------

Пример простой программы на С++, которая печатает "Привет, Мир!":

.. code-block:: c

	#include <iostream>

	int main()
	{
	    std::cout << "Hello, World!" << std::endl;
	    return 0;
	}

Для вывода здесь используется стандартная библиотека `iostream`, поток вывода `cout`. 

Исполняемые операторы в программах на С++ не могут быть сами по себе — они должны быть обязательно заключены в *функции*.

Функция `main()` — это *главная функция*, выполнение программы начинается с её вызова и заканчивается выходом из неё.
Возвращаемое значение `main()` в случае успешных вычислений должно быть равно 0, что значит "ошибка номер ноль", то есть "нет ошибки". В противном процесс, вызвавший программу, может посчитать её выполнившейся с ошибкой.

Чтобы выполнить программу, нужно её сохранить в текстовом файле `hello.cpp` и скомпилировать следующей командой:

.. code-block:: bash

	$ g++ -o hello hello.cpp

Опция `-o` сообщает компилятору, что итоговый исполняемый файл должен называться `hello`. `g++` — это компилятор языка C++, входящий в состав проекта GCC (GNU Compiler Collection). `g++` не является единственным компиляторм языка C++. Помимо него в ходе курса мы будет использовать компилятор `clang`, поскольку он обладает рядом преимуществ, из которых нас больше всего интересует одно — этот компилятор выдаёт более понятные сообщения об ошибках по сравнению с `g++`.

Упражнение №1
-------------

Скомпилируйте и выполните данную программу.

Ввод и вывод на языке С++
=========================

В Python и в С ввод и вывод синтаксически оформлены как вызов функции, а в С++ — это *операция* над объектом специального типа — *потоком*.

Потоки определяются в библиотеке `iostream,` где определены операции ввода и вывода для каждого встроенного типа.

Вывод
-----

Все идентификаторы стандартной библиотеки определены в пространстве имен `std`, что означает необходимость обращения к ним через квалификатор `std::`.

.. code-block:: c

	std::cout << "mipt";
	std::cout << 2018;
	std::cout << '.';
	std::cout << true;
	std::cout << std::endl;

Заметим, что в С++ мы не прописываем типы выводимых значений, компилятор неким (пока непонятным) способом разбирается в типе выводимого значения и выводит его соответствующим образом.

Вывод в один и тот же поток можно писать в одну строчку:

.. code-block:: c

	std::cout << "mipt" << 2018 << '.' << true << std::endl;

Для вывода в поток ошибок определён поток `cerr`.

Ввод
----

Поток ввода с клавиатуры называется `cin`, а считывание из потока производится другой операцией — `>>` :

.. code-block:: c

	std::cin >> x;

Тип считываемого значения определяется автоматически по типу переменной `x`.

Для всех типов, кроме `char`, считывание будет производиться с пропуском символов-разделителей и до следующего символа-разделителя. При этом пробел и табуляция так же, как и символ перевода каретки, являются корректными разделителями. Считывание в char происходит посимвольно независимо от типа символа.

Например для введенной строки "Иван Иванович Иванов",

.. code-block:: c

	std::string name;
	std::cin >> name;

считает в name только первое слово "Иван".

Считать всю строку целиком можно с помощью функции `getline()`:

.. code-block:: c

	std::string name;
	std::getline(std::cin, name);

Считывать несколько значений можно и в одну строку:

.. code-block:: c

	std::cin >> x >> y >> z;


Упражнение №2
-------------

Напишите программу, которая считает гипотенузу прямоугольного треугольника по двум катетам. Ввод и вывод стандартные.

+--------+---------+
| Ввод   | Вывод   |
+--------+---------+
| 3 4    | 5       |
+--------+---------+


Этапы сборки: препроцессинг, компиляция, компоновка
===================================================

Компиляция исходных текстов на Си в исполняемый файл происходит в три этапа.

.. image:: {filename}/images/lab1/lab1_1.png

Препроцессинг
-------------

Эту операцию осуществляет текстовый препроцессор.

Исходный текст частично обрабатывается — производятся:

#. Замена комментариев пустыми строками
#. Текстовое включение файлов — `#include`
#. Макроподстановки — `#define`
#. Обработка директив условной компиляции — `#if`, `#ifdef`, `#elif`, `#else`, `#endif`

Компиляция
----------

Процесс компиляции состоит из следующих этапов:

#. **Лексический анализ**. Последовательность символов исходного файла преобразуется в последовательность лексем.
#. **Синтаксический анализ**. Последовательность лексем преобразуется в дерево разбора.
#. **Семантический анализ**. Дерево разбора обрабатывается с целью установления его семантики (смысла) — например, привязка идентификаторов к их декларациям, типам, проверка совместимости, определение типов выражений и т. д.
#. **Оптимизация**. Выполняется удаление излишних конструкций и упрощение кода с сохранением его смысла.
#. **Генерация кода**. Из промежуточного представления порождается объектный код.

Результатом компиляции является **объектный код**.

Объектный код — это программа на языке машинных кодов с частичным сохранением символьной информации, необходимой в процессе сборки.

При отладочной сборке возможно сохранение большого количества символьной информации (идентификаторов переменных, функций, а также типов).


Компоновка
----------

Компоновка также называется *связывание* или *линковка*. На этом этапе отдельные объектные файлы проекта соединяются в единый *исполняемый файл*.

На этом этапе возможны так называемые ошибки связывания: если функция была объявлена, но не определена, ошибка обнаружится только на этом этапе.

Упражнение №3
-------------

Выполните в консоли для ранее созданного файла hello.cpp последовательно операции препроцессинга, компиляции и компоновки:

#. Препроцессинг:

.. code-block:: bash
	
	$ g++ -E -o hello1.cpp hello.cpp

#. Компиляция:

.. code-block:: bash

	$ g++ -c -o hello.o hello1.cpp

#. Компоновка:

.. code-block:: bash

	$ g++ -o hello hello.o

Принцип раздельной компиляции
=============================

Компиляция — алгоритмически сложный процесс, для больших программных проектов требующий существенного времени и вычислительных возможностей ЭВМ. Благодаря наличию в процессе сборки программы этапа компоновки (связывания) возникает возможность *раздельной компиляции*.

В модульном подходе программный код разбивается на несколько файлов `.cpp`, каждый из которых компилируется отдельно от остальных.

Это позволяет значительно уменьшить время перекомпиляции при изменениях, вносимых лишь в небольшое количество исходных файлов. Также это даёт возможность замены отдельных компонентов конечного программного продукта, без необходимости пересборки всего проекта.


Пример модульной программы с раздельной компиляцией на С++
==========================================================

Рассмотрим пример: есть желание вынести часть кода в отдельный файл — пользовательскую библиотеку.

program.cpp
-----------

.. code-block:: cpp

	#include "mylib.hpp"
	const int MAX_DIVISORS_NUMBER = 10000;

	int main()
	{
	    int number = read_number();

	    int Divisor[MAX_DIVISORS_NUMBER];
	    int Divisor_top = 0;
	    factorize(number, Divisor, &Divisor_top);

	    print_array(Divisor, Divisor_top);
	    return 0;
	}

 
Подключение пользовательской библиотеки в С++ на самом деле не так просто, как кажется.

Сама библиотека должна состоять из двух файлов: `mylib.hpp` и `mylib.cpp`:

mylib.hpp
---------

.. code-block:: cpp

	#ifndef MY_LIBRARY_H_INCLUDED
	#define MY_LIBRARY_H_INCLUDED

	#include <cstdlib>

	//считываем число
	int read_number();

	//получаем простые делители числа
	// сохраняем их в массив, чей адрес нам передан
	void factorize(int number, int *Divisor, int *Divisor_top);

	//выводим число
	void print_number(int number);

	//распечатывает массив размера A_size в одной строке через TAB
	void print_array(int A[], size_t A_size);

	#endif // MY_LIBRARY_H_INCLUDED


mylib.cpp
---------

.. code-block:: cpp

	#include <iostream>

	#include "mylib.hpp"

	//считываем число
	int read_number()
	{
	    int number;
	    std::cin >> number;
	    return number;
	}

	//получаем простые делители числа
	// сохраняем их в массив, чей адрес нам передан
	void factorize(int x, int *Divisor, int *Divisor_top)
	{
	    for (int d = 2; d <= x; d++) {
	        while (x%d == 0) {
	            Divisor[(*Divisor_top)++] = d;
	            x /= d;
	        }
	    }
	}

	//выводим число
	void print_number(int number)
	{
	    std::cout << number << std::endl;
	}

	//распечатывает массив размера A_size в одной строке через TAB
	void print_array(int A[], size_t A_size)
	{
	    for(int i = A_size-1; i >= 0; i--)
	    {
	        std::cout << A[i] << '\t';
	    }
	    std::cout << std::endl;
	}
 

Препроцессор С++, встречая `#include "mylib.hpp"`, полностью копирует содержимое указанного файла (как текст) вместо вызова директивы. Благодаря этому на этапе компиляции не возникает ошибок типа Unknown identifier при использовании функций из библиотеки.

Файл `mylib.cpp` компилируется отдельно.

А на этапе компоновки полученный файл `mylib.o` должен быть включен в исполняемый файл `program`.

Cреда разработки обычно скрывает весь этот процесс от программиста, но для корректного анализа ошибок сборки важно представлять себе, как это делается.

Упражнение №4
-------------

Давайте сделаем это руками:

.. code-block:: bash

	$ g++ -c mylib.cpp                      # 1
	$ g++ -c program.cpp                    # 2
	$ g++ -o program mylib.o program.o      # 3

Теперь, если изменения коснутся только `mylib.cpp`, то достаточно выполнить только команды 1 и 3.
Если только program.cpp, то только команды 2 и 3.
И только в случае, когда изменения коснутся интерфейса библиотеки, т.е. заголовочного файла `mylib.hpp`, придётся перекомпилировать оба объектных файла.

Утилита make и Makefile
=======================

Утилита `make` предназначена для автоматизации преобразования файлов из одной формы в другую.
По отметкам времени каждого из имеющихся объектных файлов (при их наличии) она может определить, требуется ли их пересборка.

Правила преобразования задаются в скрипте с именем `Makefile`, который должен находиться в корне рабочей директории проекта. Сам скрипт состоит из набора правил, которые в свою очередь описываются:

1) целями (то, что данное правило делает);
2) реквизитами (то, что необходимо для выполнения правила и получения целей);
3) командами (выполняющими данные преобразования).

В общем виде синтаксис Makefile можно представить так:

.. code-block:: text

	# Индентация осуществляется исключительно при помощи символов табуляции,
	# каждой команде должен предшествовать отступ
	<цели>: <реквизиты>
		<команда #1>
		...
		<команда #n>

То есть, правило make это ответы на три вопроса:

	{Из чего делаем? (реквизиты)} ---> [Как делаем? (команды)] ---> {Что делаем? (цели)}

Несложно заметить что процессы трансляции и компиляции очень красиво ложатся на эту схему:

	{исходные файлы} ---> [трансляция] ---> {объектные файлы}

	{объектные файлы} ---> [линковка] ---> {исполнимые файлы}

Простейший Makefile
-------------------

Для компиляции `hello.cpp` достаточно очень простого мэйкфайла:

.. code-block:: make

	hello: hello.cpp
		gcc -o hello hello.cpp

Данный Makefile состоит из одного правила, которое в свою очередь состоит из цели — `hello`, реквизита — `hello.cpp`, и команды — `gcc -o hello hello.cpp`.

Теперь, для компиляции достаточно дать команду `make` в рабочем каталоге. По умолчанию `make` станет выполнять самое первое правило, если цель выполнения не была явно указана при вызове:

	$ make <цель>

Makefile для модульной программы
--------------------------------

.. code-block:: make

	program: program.o mylib.o
	        g++ -o program program.o mylib.o

	program.o: program.cpp mylib.hpp
	        g++ -c program.cpp

	mylib.o: mylib.cpp mylib.hpp
	        g++ -c hylib.cpp


Попробуйте собрать этот проект командой `make` или `make hello`.
Теперь измените любой из файлов `.cpp` и соберите проект снова. Обратите внимание на то, что во время повторной компиляции будет транслироваться только измененный файл.

После запуска `make` попытается сразу получить цель `program`, но для ее создания необходимы файлы `program.o` и `mylib.o`, которых пока еще нет. Поэтому выполнение правила будет отложено и `make` станет искать правила, описывающие получение недостающих реквизитов. Как только все реквизиты будут получены, `make`вернется к выполнению отложенной цели. Отсюда следует, что `make` выполняет правила рекурсивно.

Фиктивные цели
--------------

На самом деле в качестве make целей могут выступать не только реальные файлы. Все, кому приходилось собирать программы из исходных кодов, должны быть знакомы с двумя стандартными в мире UNIX командами:

.. code-block:: bash

	$ make
	$ make install

Командой make производят компиляцию программы, командой `make install` — установку. Такой подход весьма удобен, поскольку все необходимое для сборки и развертывания приложения в целевой системе включено в один файл (забудем о скрипте `configure`). Обратите внимание на то, что в первом случае мы не указываем цель, а во втором целью является вовсе не создание файла `install`, а процесс установки приложения в систему. Проделывать такие фокусы нам позволяют так называемые фиктивные (phony) цели. Вот краткий список стандартных целей:

    all — является стандартной целью по умолчанию. При вызове make ее можно явно не указывать;
    clean — очистить каталог от всех файлов полученных в результате компиляции;
    install — произвести инсталляцию;
    uninstall — и деинсталляцию соответственно.


Для того чтобы make не искал файлы с такими именами, их следует определить в `Makefile`, при помощи директивы `.PHONY`. Далее показан пример `Makefile` с целями `all`, `clean`, `install` и `uninstall`:

.. code-block:: make

	.PHONY: all clean install uninstall
		
	all: program
		
	clean:
		rm -rf mylib *.o	
	program.o: program.cpp mylib.hpp
		gcc -c -o program.o program.cpp
	mylib.o: mylib.cpp mylib.hpp
		gcc -c -o mylib.o mylib.cpp
	program: program.o mylib.o
		gcc -o mylib program.o mylib.o
	install:
		install ./program /usr/local/bin
	uninstall:
		rm -rf /usr/local/bin/program

Теперь мы можем собрать нашу программу, произвести ее инсталлцию/деинсталляцию, а так же очистить рабочий каталог, используя для этого стандартные make цели.

Обратите внимание на то, что в цели `all` не указаны команды; все что ей нужно — получить реквизит `program`. Зная о рекурсивной природе make, не сложно предположить, как будет работать этот скрипт. Также следует обратить особое внимание на то, что если файл `program` уже имеется (остался после предыдущей компиляции) и его реквизиты не были изменены, то команда `make` ничего не станет пересобирать. Это классические грабли make. Так, например, изменив заголовочный файл, случайно не включенный в список реквизитов (а надо включать!), можно получить долгие часы головной боли. Поэтому, чтобы гарантированно полностью пересобрать проект, нужно предварительно очистить рабочий каталог:

.. code-block:: bash

	$ make clean
	$ make

P.P.S. Неплохая `статья`__ с описанием мейкфайлов.

.. __: https://habrahabr.ru/post/155201/


Типы данных
-----------

В языках С++ все переменные имеют определенный тип данных. Например, переменная, имеющая целочисленный тип не может содержать ничего кроме целых чисел, а переменная с плавающей точкой — только дробные числа.

Тип данных присваивается переменной при ее объявлении или инициализации. Ниже приведены основные типы данных языка C++:

#. `int` — целочисленный тип данных.
#. `float` — тип данных с плавающей запятой. Обычно 32-битный тип с плавающей точкой формата `IEEE-754`.
#. `double` — тип данных с плавающей запятой двойной точности. Обычно 64-битный тип с плавающей точкой формата `IEEE-754`.
#. `long double` - тип с плавающей точкой повышенной точности. Не обязательно отображается на типы `IEEE-754`. Обычно 80-битный тип с плавающей точкой формата x87 на архитектурах `x86` и `x86-64`.
#. `char` — символьный тип данных.
#. `bool` — логический тип данных, cпособный хранить одно из двух значений: `true` (истина) или `false` (ложь).


Базовым типом для целочисленных данных является int. Mожет быть опущен, если представлен любой из модификаторов. Если не представлен ни один из модификаторов размера, гарантировано имеет ширину не меньше 16 бит. Тем не менее, на 32/64-битных системах почти всегда имеет ширину не меньше 32 бит.

Возможные модификаторы:
#. `signed` — целевой тип будет иметь знаковое представление (по умолчанию, если не представлен ни один из вариантов).
#. `unsigned` — целевой тип будет иметь беззнаковое представление.
#. `short` — целевой тип будет оптимизирован по размеру и иметь ширину не меньше 16 бит.
#. `long` — целевой тип будет иметь ширину не меньше 32 бит.
#. `long long` — целевой тип будет иметь ширину не меньше 64 бит. (начиная с C++11)

Возможные варианты перечислены ниже: 


+------------------------+------------------------+-------------------+
| Спецификатор типа      | Эквивалетный тип       | Размер в битах    |
+========================+========================+===================+
| short                  |                        |                   |
| short int              |     short int          |                   |
| signed short           |                        |                   |
| signed short int       |                        |     >= 16         |
+------------------------+------------------------+                   |
| unsigned short         | unsigned short int     |                   |
| unsigned short int     |                        |                   |
+------------------------+------------------------+-------------------+
| int                    |                        |                   |
| signed                 |       int              |                   |
| signed int             |                        | >= 16 (обычно 32) |
+------------------------+------------------------+                   |
| unsigned               |     unsigned int       |                   |
| unsigned int           |                        |                   |
+------------------------+------------------------+-------------------+
| long                   |                        |                   |
| long int               |      long int          |                   |
| signed long            |                        |                   |
| signed long int        |                        |     >= 32         |
+------------------------+------------------------+                   |
| unsigned long          | unsigned long int      |                   |
| unsigned long int      |                        |                   |
+------------------------+------------------------+-------------------+
| long long              |                        |                   |
| long long int          |    long long int       |                   |
| signed long long       |      (C++11)           |                   |
| signed long long int   |                        |     >= 64         |
+------------------------+------------------------+                   |
| unsigned long long     | unsigned long long int |                   |
| unsigned long long int |     (с++11)            |                   |
+------------------------+------------------------+-------------------+


Для определения размера (в байтах), занимаемого тем или иным типом используется оператор `sizeof(тип)`. Например: `sizeof(int)`.


Упражнение №5
-------------

Напишите программу, которая выводит на экран размер памяти, занимаемый типами `char`, `bool`, `short`, `int`, `long`, `long long`.


Объявление переменных
=====================

Объявление переменной в C++ происходит таким образом: сначала указывается тип данных для этой переменной, затем название этой переменной. При обьявлении также можно присвоить начальное значение.

Например:

.. code-block:: cpp

	int a; // объявление переменной a целого типа. 
	float b; // объявление переменной b типа данных с плавающей запятой. 
	float c = 14.2; // инициализация переменной типа float. 
	char e = 'a'; // инициализация переменной типа char. 
	bool f = false; // инициализация логической переменной k.

Присваивание используется для сохранения определенного значение в переменной. Например, запись вида `a = 5` задает переменной `a` значение 5.

Циклы `for` и `while`
=====================

Синтаксис оператора `for` выглядит так:

.. code-block:: cpp

	for (действие до начала цикла; условие продолжения цикла; действия в конце каждой итерации цикла) {
		инструкция цикла 1;
		инструкция цикла 2;
		..............
		инструкция цикла N;
	}


Частный случай использования выглядит так:

.. code-block:: cpp

	for (счетчик = значение; счетчик < значение; шаг цикла) {
		тело цикла;
	}


Программа, которая выводит числа от 0 до 9 выглядит так:

.. code-block:: cpp

	#include <iostream>
	using namespace std;

	int main() 
	{
		int i; // счетчик цикла
		for (i = 0; i < 10 i++) // задаем начальное значение 0, конечное 9 и задаем шаг цикла - 1.
		{
			out << i << ' ';
		}
		cout << endl;
		return 0;
	}


Если скомпилировать и запустить программу, то она выведет последовательно числа от 0 до 9. Конечное значение счетчика задано строгим неравеством (`i < 10`), поэтому цикл прекращает свое выполнение по достижении значение 10 переменной `i`. Если заменить строгое неравество на нестрогое (`i <= 10`), то будут выведены числа от 0 до 10.

Синтаксис цикла `while` выглядит так:

.. code-block:: cpp

	while (Условие) {
		инструкция цикла 1;
		инструкция цикла 2;
		..............
		инструкция цикла N;
	}

Цикл будет выполняться, пока условие, указанное в круглых скобках является истиной.  Программа, выводящая числа от 0 до 9 с использованием `while` будет выглядеть так:

.. code-block:: cpp

	#include <iostream>
	using namespace std;

	int main() 
	{
		int i = 0; // инициализируем счетчик цикла
		while(i < 10)
		{
			out << i << ' ';
			++i;
		}
		cout << endl;
		return 0;
	}

При использовании `while` важно не забывать инициализировать счетчик цикла перед циклом, и увеличивать его значение внутри.

Для завершения исполнения цикла раньше времени используется `break`:

.. code-block:: cpp

	#include <iostream>
	using namespace std;

	int main() 
	{
		int i = 0; 
		while(i < 10)
		{
			if ( i == 5 ) {
				break;
			}
			out << i << ' ';
		}
		cout << endl;
		return 0;
	}

В данном случае программа напечатает числа от 0 до 4.

Для перехода на следующую итерацию цикла без исполнения части кода тела цикла используется `continue`:

.. code-block:: cpp

	#include <iostream>
	using namespace std;

	int main() 
	{
		int i = 0; 
		while(i < 10);
		{
			if ( i == 5 ) {
			++i;
				continue;
			}
			out << i << ' ';
			++i;
		}
		cout << endl;
		return 0;
	}

Программа напечатает числа от 0 до 9, за исключением 5. Заметим, чтобы цикл не правратился в бесконечный, увеличение переменной `i` происходит в двух местах тела цикла.


Генерация псевдослучайных чисел
===============================

Псевдослучайные числа в языке программирования С++ могут быть сгенерированы функцией `rand()` из стандартной библиотеки С++. Функция rand() генерирует числа в диапазоне от 0 до `RAND_MAX`, где `RAND_MAX` — это константа, определённая в библиотеке `<cstdlib>`. 

.. code-block:: cpp

	#include <iostream>
	using namespace std;
 
	int main(int argc, char* argv[])
	{
		cout << "RAND_MAX = " << RAND_MAX << endl;
		cout << "random number = " << rand() << endl;
		return 0;
	}


Упражнение №6
-------------

#. Сгенерируйте последовательность из 10ти случайных целых чисел в интервале [1, 6]
#. Сгенерируйте последовательность из 10ти случайных чисел с плавающей запятой в интервале [1,3]


Запись в файл `ofstream`
========================

Для работы с файлами необходимо подключить заголовочный файл `<fstream>`. В `<fstream>`` определены несколько классов и подключены заголовочные файлы `<ifstream>`` — файловый ввод и  `<ofstream>`  — файловый вывод.

Файловый ввод/вывод аналогичен стандартному вводу/выводу (`cin`/`cout`), c единственным отличием  - ввод/вывод выполнятся не на экран, а в файл. Ввод/вывод на стандартные устройства выполняется с помощью объектов `cin` и `cout`, а для организации файлового ввода/вывода достаточно создать собственные объекты, которые можно использовать аналогично операторам `cin` и `cout`.

Например, чтобы создать текстовый файл и записать в него строку "Hello world!" необходимо проделать следующие шаги:

#. создать объект класса `ofstream`;
#. связать объект класса с файлом, в который будет производиться запись;
#. записать строку в файл;
#. закрыть файл.


.. code-block:: cpp

	#include <fstream>
	using namespace std;
 
	int main(int argc, char* argv[])
	{
		ofstream fout;
		fout.open("hello.txt");
		fout << "Hello world";
		fout.close();
		return 0;
	}

Аналогично с помощью `ifstream` можно производить чтение из файла. 

Шаги 1 и 2 можно объединить, и в итоге получим:

.. code-block:: cpp

	#include <fstream>
	using namespace std;
 
	int main(int argc, char* argv[])
	{
		ofstream fout("hello.txt");
		fout << "Hello world";
		fout.close();
		return 0;
	}

Считывание данных из файла можно производить c разделением по словам или построчно:

.. code-block:: cpp

	#include <fstream>
	#include <iostream>
	using namespace std;
 
	int main(int argc, char* argv[])
	{
		ifstream fin("hello.txt"); 
		char buff[64];  // буфер, в который записываются считываемые слова

		while(!fin.eof()) {  // выполняем пока не достигнут конец файла
			fin >> buff;            // считываем слово в буфер
 			cout << buff << endl;   // выводим на экран
 		}

		fin.close();
		return 0;
	}


.. code-block:: cpp

	#include <fstream>
	#include <iostream>
	using namespace std;
 
	int main(int argc, char* argv[])
	{
		ifstream fin("hello.txt"); 
		char buff[64];  // буфер, в который записываются считываемые слова

		while(!fin.eof()) {  // выполняем пока не достигнут конец файла
			fin.getline(buff, sizeof(buff));  // считываем строку в буфер
 			cout << buff << endl;             // выводим на экран
 		}

		fin.close();
		return 0;
	}



Упражнение №7
-------------

#. Напишите программу которая генерит случайным образом три целых числа  в интервале от 0 до 100 - `a`, `d`, `n`. Записывает эти три числа в текcтовый файл. Далее генерит `n` элементов арифметической последовательности с начальным элементом `a` и разностью `d` и также записывает их в текстовый файл.
#. Напишите программу которая генерит случайным образом два числа с плавающей запятой в интервале от 1 до 5 - `b`, `q`. Записывает эти два числа в текcтовый файл. Далее генерит `10` элементов геометрической последовательности с начальным элементом `b` и знаменателем `q` и также записывает их в текстовый файл.


