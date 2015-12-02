# Elm

## Core Language

- value
- functions
- lists
- tuples
- records

### Values

конкатенация строк через _++_

```elm
"hello" ++ " world"
```

> "hello world"

Целочисленное деление как в Python _//_

```elm
9 / 2
```

>4.5

```elm
9 // 2
```

>4


### Functions

```elm
isNegative n = n < 0
```

аргументы передаются как в Haskel через пробел `max 5 3`

#### if Expressions

```elm
if True then "hello" else "world"
```


### List

одна из наиболее распространненых структур в Elm.
Их роль схожа с ролью массивов в JS.

Последовательность сущностей одного типа.

В контрасте с ООП языками, Elm не имеет концепции "методов",
где в объектах хранятся данные и логика.
В Elm данные и функции существуют раздельно.
Поэтому когда мы говорим `List.isEmpty`, мы используем функцию
`isEmpty` из модуля `List`

```elm
numbers = [ 1, 2, 3, 4 ]

double n = n * 2

List.map double numbers
```

> [2, 4, 6, 8]

[Модуль List](http://package.elm-lang.org/packages/elm-lang/core/3.0.0/List)


### Tuples

Кортежи содержат фиксированное количество элементов
различного типа в отличии от списков.
Обычно применяются для возвращения функцией нескольких значений.

```elm

import String

goodName name =
  if String.length name <= 20 then
    (True, "name accepted!")
  else
    (False, "name was to long")

goodName "Tom"
```
> (True, "name accepted!")

Это может быть очень удобно, но для когда сущности становятся 
более сложными, лучше использовать _Records_


### Records

Записи это набор пар ключ-значение.

```elm
point = { x = 3, y = 4 }

point.x
```

> 3

```elm
user = { name = "Egor" , age = 25 }

user.name
```

> "Egor"


Мы можем создать запись использую фигурные скобки,
и для досупа к полям использовать точку.
Также в Elm есть версия записи которая работает как функция.
Ставя в начале переменной точку, вы пытаетесь получуть доступ к переменной.

```elm
.name user
```

> "Egor"

```elm
List.map .name [user, user, user]
```

> ["Egor", "Egor", "Egor"]


Когда приходится делать функции с записями вы можете
использовать такой паттерн

```elm
under30 {age} = age < 30

under30 user
```

> True

```elm
under30 { abracadabra = "TrahTiBiDah", age = 3421 }
```

> False

Мы пожем передать запись любой длинны которая содержит поле
_age_ содержащее число.

Часто полезно изменить значение в записи.

```elm
{ user | name = "Alex" }
{ user | age = 35 }
```

> { age = 35, name = "Alex"}

Важно отметить, что мы не делаем _деструктивное_ изменение.
Другими словами, мы не обновляем значения, а создаем новые записи, которые перезаписывают старые.


#### Сравнение Records and Objects

Записи схожи с объектами в JS. Но есть несколько критических отличий:

- Вы не можете запросить несуществующее поле
- Нет полей равных _null_ или _undefined_
- Вы не можете делать рекурсивные записи с ключевыми словами _this_ или _self_

Elm поощряет строгое разделение данных и логики, и способность
всегда знать что есть что. Намерено избегая системной проблемы ООП языков.
Также Elm поддерживает [structural type](https://en.wikipedia.org/wiki/Structural_type_system), что дает возможность использовать записи в любой ситуации, пока существуют необходимые поля.


## Model The Problem

Многие языки имеют проблемы при описании данных странными формами.
В этой секции мы научимся работать с данными правильно.
Мы начнем в концепции "контракты" и продолжим дальше _сходить с ума_.


### Contracts

Типы очень важный инструмент для моделирования. Думайте о них как о "конрактах" которые могут быть проверены компиляторм, который говорит: "Я принимаю только строковые аргументы"

Способ которым мы будет заключать контракт "type annotations",
где мы определяем форму данных с которыми будем работать.

```elm
fortyTwo : Int
fortyTwo = 42

names : List String
names = [ "haha", "it's", "working"]

book : { title: String, author: String, pages: Int }
book = { title = "Demian", author = "Hesse", pages = 176 }
```

Здесь мы просто описали основные формы наших данных.
Число, список строк, запись с полями разных типов - ничего необычного пока. Это станет особенно ценным когда мы начнем это использовать с функциями.

```elm
import String

longestName : List String -> Int
longestName names = List.maximum (List.map String.length names)

isLong : { record | pages : Int } -> Bool
isLong book = book.pages > 400
```


### Enumerations

Часто требуется создать тип в котором перечисляются несколько возможных состояний. Представьте мы создаем _todolist_ и нам необходимо создать фильтр какие задачи отображають: все задания, активные задания или выполненные.
Все три состояние мы можем описать так:

```elm
type Visibility = All | Active | Completed
```

Теперь используем *case-expressions*

```elm
toString : Visibility -> String
toString visibility = 
  case visibility of
    All ->
      "All"
    Active ->
      "Active"
    Completed ->
      "Completed"
```

это выполняет туже роль что _enumerations_ в таких языках как Java или C++.


### State Machines

Хорошо, а что если мы хотим представить когда кто-то залогинен или нет? Мы можем создать небольшую State Machine (когда поведение варьируется в зависимости от состояния).

```elm
type User = Anonymous | LoggedIn String
```

Обратите внимание, что _LoggedIn_ ассоциирован с дополнительной информацией. Когда пользователь _LoggedIn_ мы знаем его имя.

```elm
userPhoto : User -> String
userPhoto user =
  case user of
    Anonymous ->
      "anon.png"
    LoggedIn name ->
      "users/" ++ name ++ "/photo.png"
```

Теперь представим, что у нас есть несколько пользователей и мы хотим отобразить иx фото.

```elm
activeUsers : List User
activeUsers =
  [ Anonymous
  , LoggedIn "Tom"
  , LoggedIn "Steve"
  , Anunymous
  ]

 photos = List.map userPhoto activeUsers
 ```
 > photos = [
 >   "anon.jpg"
 >   , "users/Tom/photo.png"
 >   , "users/Steve/photo.png"
 >   , "anon.png"
 >   ]



### Tagged Unions 

> [Тип-сумма](https://en.wikipedia.org/wiki/Tagged_union), также иногда называют [алгебраический тип данных](https://en.wikipedia.org/wiki/Algebraic_data_type)

Теперь попробуем объдинить несколько различных типов в один.

Скажем мы делаем панель приборов с тремя различными виджетами.
Один отображает диаграмму рассеяния, другой лог-данные, третий временной ряд.

```elm
type Widget
  = ScatterPlot (List (Int, Int))
  | LogData (List String)
  | TimePlot (List (Time, Int))
```

Вы можете представлять это как складывание вместе три разных типа. Каждый тип _tagged_ с именем как _LogData_. Это даст нам возможность отличать их друг от друга.

```elm
view : Widget -> Element
view widget =
  case widget of
    ScatterPlot points ->
      viewScatterPlot points
    LogData logs ->
      flow down (map viewLog logs)
    TimePlot occurances ->
      viewTimePlot occurances
```

В зависимости от типа виджета мы по разному их рендерим.
Допустим на необходимо расширить виджет.

```elm
type Scale = Normal | Logaritmic

type Widget
  = ScaterPlot (List (Int, Int))
  | LogData (List String)
  | TimePlot Scale (List (Time, Int))
 ```

 Все эти стратегии могут быть использованы когда вы делаете игру с разными типами плохих парней. 


### Banishin NULL

Большинство языков используют концепцию _null_. Каждый раз когда вы ждете строку вы можете получить пустую строку. Надо ли это проверять?

Elm обходит эту проблему стороной, используя тип *Maybe*.

```elm
type Maybe a = Just a | Nothing
```

Важно отметить что этот тип принимает аргумент любого типа.

```elm
String.toInt : String -> Result String Int

toMonth : String -> Maybe Int
toMonth rawString =
  case String.toInt rawString of
    Err message ->
      Nothing
    Ok n ->
      if n > 0 && n <= 12 then
        Just n
      else
        Nothing
 ```

Контракт с _toMonth_ говорит всем, что будет возвращать Int или нет. Вам не надо переживать за нулевые значения.


### Recursive Data Strictures

Если вы имели дело со [связанными списками](https://en.wikipedia.org/wiki/Linked_list) в С или Java. Вы удивитесь как легко с ними работаь в Elm.

```elm
type List a = Empty | Node a (List a)
```

..............

