## The Elm Architecture


Архитектура Elm приложений является простым паттерном для бесконечно вложенных компонентов.
Он отлично подходит для модульности, повторного использования кода и тестирования. 
На восьми простых примерах мы постепенно изучим основные принципы построение Elm приложений.

1. Conter
2. Pait of counters
3. List of counters
4. List of counter (variation)
5. GIF fetcher
6. List of GIF fetcher
7. Pair of animating squares



### The Basic Patter


Логика каждого Elm приложения разделена на три части 

- model
- update
- view

```elm

-- MODEL

type alias Model = { ... }


-- UPDATE

type Action = Reset | ...

update : Action -> Model -> Model
update action model =
  case action of
    Reset -> ...
    ...


-- VIEW

view : Modtl -> HTML
view = 
  ...

  ```


### Пример 1: A counter

Наш первый пример простой счетчик который можно увеличивать или уменьшать.

```elm
type alise Model = Int
```

для обновления нашей модели


```elm
type Action = Increment | Decrement

update : Action -> Model -> Model
update action =
  case action of
    Increment -> model + 1
    Decrement -> model - 1
    ```

```elm
view : Signal.Adress Action -> Model -> Html
view address model =
  div []
    [ button [ onClick addres Decrement ] [text "-" ]
    , div [ countStyle ] [ text (toString mode) ]
    , button [ onClick address Increment ] [ text "+" ]
    ]

countStyle : Attribute
countStyle =
...
```


#### Запуск программы

```elm
  import Counter exposing (update, view)
  import StartApp.simple exposing (star)

main =
 start { model = 0, update = update, view = view }
 ```

Мы используем StartApp модуль чтобы соединить нашу модель с
обновлением и представлением.
Это небольшая обертка вокруг Elm сигналов.


----------------------------



