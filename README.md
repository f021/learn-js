> записи при чтении [Javascript Allonge](https://leanpub.com/javascript-allonge),
Reg “raganwald” Braithwaite



### Closures and Scope
```
((x) => (y) => x)(1,2)
```
> 1

**Замыкание** (англ. closure) в программировании — функция,
в теле которой присутствуют ссылки на переменные,
объявленные вне тела этой функции в окружающем коде
и не являющиеся её параметрами. Говоря другим языком,
замыкание — функция, которая ссылается на свободные
переменные в своём контексте.
[Wiki](https://goo.gl/dmSERC)

**Свободная переменная** — переменная, которая:
встречается в теле функции или предложения,
но которая не является параметром этой функции,
и/или определена в месте, находящимся
где-то за пределами функции.
[Wiki](https://goo.gl/DESDX2)


*чистая функция может содержать замыкание*


**I Combinator**

`(x) => x;`

**K Combinator**

`(x) => (y) => x;`

### Каррирование и частичное применение

**Каррирование** или карринг (англ. currying) в информатике
— преобразование функции от многих аргументов в функцию,
берущую свои аргументы по одному. Это преобразование было
введено М. Шейнфинкелем и Г. Фреге и получило свое название
в честь Х. Карри.
[Wiki](https://goo.gl/6HJQO5)
```
(x) =>
  (y) =>
    (z) => x + y + z;
```

> call (1)(2)(3)

`(x, y, z) => x + y + z;`

> call (1, 2, 3)

вызов каррированой ф-и с некоторыми аргументами называют
**частичным применением**
[ibm](https://www.ibm.com/developerworks/ru/library/j-jn9/)


### Функции высшего порядка

**Функция высшего порядка** — функция, принимающая в качестве
аргументов другие функции или возвращающая другую функцию
в качестве результата. Основная идея состоит в том, что функции
имеют тот же статус, что и другие объекты данных. Использование
функций высшего порядка приводит к абстрактным и компактным
программам, принимая во внимание сложность производимых
ими вычислений
[Wiki](https://goo.gl/)
```
const repeat = (num, fn) =>
  (num > 0)
    ? (repeat(num - 1), fn), fn(num)
    : undefined;

repeat(3, function(n) {
  console.log(`Hello ${n}`)
});
```
 
>'Hello 3'

>'Hello 2'

>'Hello 1'

>undefined



### Комбинаторы
_cпецифичекий тип функций высшего порядка_


Комбинаторы это просто.
[Викиучебник](https://goo.gl/vdgjdC)

Комбинаторная логика[ Wiki](https://goo.gl/4dOyay)

В книге Javscript Allonge автор предлагает 'лузерское' значение
комбинаторов:
*Чистые функции высшего порядка, которые принимают в качестве
аргументов функции и вовзращают фукнции.*


**Compose or B combinator**

Первый комбинатор программисты называют _композицией_,
в логиге называют B комбинатором или "синией птицей"
автор рекомендует книгу с логическиеми задачами 
["To Mock a Mockingbird"](https://en.wikipedia.org/wiki/To_Mock_a_Mockingbird)

```
const compose = (a, b) => 
  (c) => (a(b(c))
```


## Функции декораторы
_Функции высшего порядка принимающие как аргумент одну ф-ю
и возвращающие другую
что позволяет изменить или расширить ее поведение_

`const not = (fn) => (x) => !fn(x)`


###Композиция###

```
const cookAndEat = (food) => eat(cook(food))

const compose = (a, b) => (c) => a(b(c))

const cookAndEat = compose(eat, cook);
```


### Once и Maybe декораторы

_once_ - ф-я выполяющая только один раз

_maybe_ - ф-я ничего не выполняет если ей ничего не передается (как null или undefined)

конечно можно использовать if, но once и maybe возможно
вызывать цепочкой

```
const actuallyTransfer = (from, to, amout) =>
  // do something

const invokeTransfer = once(mayby(actuallyTransfer(...)));
```

расмотрим частиное применение

ф-я в библиотеке Undescore

`_.map([1, 2, 3], (n) => n * n );`

> [1, 4, 9]

```
const mapWith = (fn) =>
  (array) => _map(array, fn);

const squareAll = mapWith((n) => n * n);

squareAll([1, 2, 3]);
```

важно отметить, что частичное применения ортогонально композии
и они вместе отлично работают

```
const safeSquareAll = mapWith(maybe((n) => n * n));

safeSquareAll([1, 2, null, 3]);
```

> [1, 4, null, 9]


примеры частичного применения ф-и с выполнением
аргумента слева или справа

```
const callFirst = (fn, larg) =>
  (...rest) => fn.call(this, larg, ...rest);

const callLast = (fn, rarg) =>
  (...rest) => fn.call(this, ...rest, rarg);

const greet = (me, you) =>
  `Hello, ${you}, my name is ${me}`;

const heliosSaysHello = callFirst(greet, 'Helios');

heliosSaysHello('Eratha');
```
>'Hello, Eratha, my name is Helios'

```
const sayHelloToCeline = callLast(greet, 'Celine');

sayHelloToCeline('Eratha');
```

> 'Hello, Celine, my name is Eratha'

забегая вперед можно изменить ф-и

```
const callLeft = (fn, ...args) =>
  (...remainingArgs) =>
    fn(...args, ...remainingArgs);

const callRight = (fn, ...args) =>
  (...remainingArgs) =>
    fn(...remainingArgs, ...args);
```


###Unary

декоратор - который модифицирует к-во аргументов
полученных функцией. Unary принимает ф-и
и модифицирует ее в ф-ю принимающую один аргумент

```
const unary = (fn) =>
  fn.length === 1
    ? fn
    : function(something) {
      return fn.call(this.something);
    }
```

###Tap
K combinator
один из самых базовыx комбинаторов 
известный как "Kestrel" :)

`const k = (x) => (y) => x;`

```
const tap = (value) =>
  (fn) => (
      typeof(fn) === 'funciton' && fn(value),
      value
    )
```

_tap_ традиционное имя функции из unix
принимает значение и возвращает ф-ю
которая всегда возвращает принятое значение
но если передается функция, она выполняется
для side-effects

```
tap('espresso')((it) => {
  console.log(`our drink is ${it}`);
});
```

> => 'our drink is espresss'

такая библиотека как undescore использует
tap как не карированую ф-ю

```
_.tap('espresso', (it) => 
  console.log(`our drink is ${it}`);
);
```

давайте изменим наш вариант


```
const tap = (value, fn) => {

  const curried = (fn) => (
      typeof(fn) === 'function' && fn(value),
      value
    );

  return fn === undefined
    ? curried
    : curried(fn);

};
```