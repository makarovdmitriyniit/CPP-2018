## Тема 2. Типы, выражения, функции

### Объявления и инициализация переменных

Классическое выражение для объявления переменной в стандарте С++98:

```c++
int a=10;
```

также разрешается инициализация с круглыми скобками:

```c++
int a(10);
```

В стандарте С++11 разрешены и другие формы:

```c++
int a={10};
int a{10};
```

В новом стандарте можно использовать ключевое слово `auto` для объявления и инициализации переменных:

```c++
auto a=10;
auto a={10};
auto a(10);
auto a{10};
```

Использование фигурных скобок может вызывать вопрос о целесообразности нового синтаксиса. На самом деле, в этом случае задействуется  `std::initializer_list<int>` из стандартной библиотеки, который обладает дополнительными проверками данных на корректность, что может оказаться полезным.

Одно из важнейших преимуществ объявления переменных с помощью `auto` - необходимость их явной инициализации. Без указания значения объявить переменную не получиться.

Можно явно узнать тип переменной:

```c++
auto a=10.;
cout<<typeid(a).name()<<endl;
```

Ответом будет строка `d`, означающая тип `double`.

#### Упражнение:

>Написать программу, в которой объявлены переменные с ключевым словом `auto` и используются константы разных типов. Проверить типы с помощью `typeid`.

### Типы данных

Когда мы создаем в программе объект, требующий памяти, то мы относим его к определенному типу. Тип задает соглашения о интерпретации значений разрядов в ячейке памяти.

Напимер, беззнаковое целое **unsigned** трактует все разряды как значение целого числа, а знаковое **signed** выделяет старший разряд как знаковый и в зависимости от его значение трактует как число с минусом или плюсом.

При попытке присвоения значения "длинной" переменной более "короткой" происходит отбрасывание лишних байтов, что вызывает потерю информации. При обратном действии, возникают лишние байты с нулевыми значениями.

Что происходит при переполнении значения ячейки? Ничего, поскольку для чисел со знаком мы переходим из положительной области значений в отрицательную, а потом наоборот.

![](./img/nums.png)

Значения меняются по кругу.

Особенностью представления вещественных чисел является приблизительная точность.

```c++
#include <iostream>
using namespace std;
int main()
{
    float a=3.3;
    cout.setf(ios::showpoint);
    cout.precision(10);
    cout<<a<<endl;
    return 0;
}
```
Ответ:

 ```
 3.299999952
 ```


### Big endian и little endian

![](./img/endian.png)

Используется два порядка расположения байтов в многобайтной величине:  от старшего к младшему **big endian** и от младшего к старшему **little endian**.


![](./img/endian2.png)

- **BE** используют: IBM 360/370/390, Motorola 68000, SPARC 
- **LE** используют: Intel x86


Существенным достоинством little-endian по сравнению с big-endian порядком записи считается возможность ''неявной типизации'' целых чисел при чтении меньшего объёма байт.

Так, если в ячейке памяти содержится число 0x00000022, то прочитав его как int16 (два байта) мы получим число 0x0022, прочитав один байт — число 0x22. Однако, это же может считаться и недостатком, потому что провоцирует ошибки потери данных.

Как проверить систему?

```c++
int endian1() {
  int one = 1;
  char *ptr;
  ptr = (char *)&one;
  return (*ptr);
}
int endian2() {
  union {
    int one;
    char ch;
  } endn;
  endn.one = 1;
  return endn.ch;
}
```

### Объявление и иницализация массивов

Массив представляет собой набор данных одного типа, расположенных в смежных ячейках памяти.

```c++
int arr[7];
```

![](./img/arr1.png)

Квадратные скобки являются признаком массива, а число в этих скобках - количество элементов, размещенных в массиве. 

Существуют также **variable length array (VLA)**, которые поддерживаются поздними стандартами языка С. Однако, при использовании **gcc(g++)** в С++ тоже можно использовать VLA (на свой страх и риск).


```c++
#include <iostream>
using namespace std;

int main()
{
    int size;
    cin>>size;
    int arr[size];
    cout<<sizeof(arr)<<endl;
    return 0;
}
```

VLA можно создавать только внутри фигурных скобок.

Адрес любого элемента массива может быть легко найден, если известен адрес первой ячейки (А0).

- A0=&arr[0]=arr
- A1=A0+1*sizeof(int)  
- A2=A0+2*sizeof(int)
- An=A0+n*sizeof(int)

Обращение к *i*-му элементу записывается в виде выражения `arr[i]`.

Следующая программа демонстрирует создание, инициализацию и обработку массива с позиций стандарта **C++11**:

```c++
#include <iostream>
using namespace std;

int main()
{
    int arr[]{1,2,3};
    for(auto item:arr) 
        cout<<item<<" ";
    cout<<endl;
    return 0;
}
```

###  Указатели

Указатели - это ячейки памяти, хранящие адреса других ячеек памяти. В этом случае говорят, что указатель ссылается или указывает на какую-то переменную.

Можно создать указатель на:

- обычную переменную;
- указатель;
- массив;
- функцию.

Примеры различных указателей:

```c++
int a=10;
int b[5][15]{};

int *pa=&a;       // указатель на int
int (*pb)[15]=b;  // указатель на массив из 5 int
int **pc=&pa;     // указатель на указатель на int
```

Главная операция при работе с указателями - **разыменование**, то есть обращение в память по адресу, хранящемуся в указателе.

Над адресами выполняются операции **адресной арифметики**: 

- сложние адреса с целым числом
- вычитание из адреса целого числа
- вычитание из адреса адреса

### Функции

Функции являются относительно самостоятельной частью программы, которую можно выполнять множество раз, задавая значения некоторых параметров. Обычно у функции есть **заголовок** и **тело**.

Пример простейшей функции:

```c++
int add(int x,int y)
{
   return x+y;
}
```

Работа функций осуществляется через стек. При вызове функции формируется **stack frame**, в котором размещаются формальные параметры, в которые копируются входные значения (фактические параметры), локальные переменные, после чего осуществляется переход управления на тело функции. После окончания работы из стека извлекаются результат работы функции (выражение в строке с **return**) и управление снова передается в точку вызова. Весь этот механизм требует определенного времени и расходует память на копирование объектов. Поэтому стараются снизить нагрузуку на функции, передавая адреса объектов, а не их копии (что имеет смысл для больших объектов).

### Категория выражения: lvalue и rvalue

Когда мы пишем некоторое выражение мы вправе задасться вопросом: какой тип у результата? Но помимо типа рассматривается еще и категория: **lvalue** или **rvalue**. 

В языке С различия между этими категориями просты:

- если выражение может стоять слева от присваивания, то оно **lvalue**.
- все остальные относятся к **rvalue**.


### Ссылки lvalue

Традиционные ссылки С++ называют теперь **lvalue-ссылками** из-за появления **rvalue-ссылок**.

### Ссылки rvalue

В стандарте **С++11** былл введен новый тип ссылок: **rvalue**, которые объявляются с двумя амперсандами.

```c++
#include <iostream>
using namespace std;

int fun()
{
    return 22;
}
int main()
{
    int a=33;
    int&& b=fun();
    cout<<b<<endl;    
    b=a;
    cout<<b<<endl;
    a=44;
    cout<<b<<endl;
    
    return 0;
}
```
При изменении `a` значение `b` не меняется, поскольку временная ячейка памяти не изменилась.

При попытке связать rvalue-ссылку с обычной переменной компилятор g++ выдает сообщение об ошибке:

```c++
#include <iostream>
using namespace std;

int main()
{
    int a=33;
    int&& b=a;
    cout<<b<<endl;   
    return 0;
}
```
```

$ g++ -std=c++11 ref2.cpp
ref2.cpp:7:11: error: rvalue reference to type 'int' cannot bind to
     lvalue of type 'int'
    int&& b=a;
          ^ ~
1 error generated.
```

### Шаблоны функций

Шаблон функций позволяет задачть описание функции без привязки к конретным типам. В место них применяют типы-параметры.

```c++
template<class T>
T square(T param)
{
   return param*param;
}
```

Вызов шаблонной функции подразумевает автоматическое определение типа параметра:

```c++
int x=2;
cout<<square(x);
```

В простейших случаях вывод нужного типа не вызывает проблем. Однако, могут встречаться и более сложные случае, особенно при использовании ссылочных типов.

#### Случай 1.  lvalue-ссылка

Ссылочная часть игнорируется

```c++
template<class T>
void printSquare(T& param)
{
   cout<<param*param;
}

int a=10;
const int b=a;
constr int& c=a;
 
f(a); // T - int,       param - int&
f(b); // T - const int, param - const int& 
f(c); // T - const int, param - const int&
```

При добавление **const** в параметр:

```c++
template<class T>
void printSquare(const T& param)
{
   cout<<param*param;
}

int a=10;
const int b=a;
constr int& c=a;
 
f(a); // T - int, param - const int&
f(b); // T - int, param - const int& 
f(c); // T - int, param - const int&
```

#### Случай 2.  rvalue-ссылка

В этом случае надо смотреть, к какому типу выражений (lvalue или rvalue) принадлежит фактический параметр.

```c++
template<class T>
void printSquare(T&& param)
{
   cout<<param*param;
}

int a=10;
const int b=a;
constr int& c=a;
 
f(a); // T - int&,       param - int&       (a - lvalue)
f(b); // T - const int&, param - const int& (b - lvalue)
f(c); // T - const int&, param - const int& (c - lvalue)
f(10);// T - int,        param - int&&      (27 - rvalue)
```