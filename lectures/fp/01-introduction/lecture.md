# Лекция 1: Введение в функциональное программирование

## 📚 Цель лекции

Понять основные принципы функционального программирования, изучить историю развития ФП и освоить базовые концепции на примерах Haskell.

## 🕐 План лекции

1. **История развития ФП**
2. **Основные принципы ФП**
3. **Преимущества и недостатки ФП**
4. **Сравнение с другими парадигмами**
5. **Практические примеры на Haskell**

---

## 🏛️ История развития ФП

### Истоки (1930-1950)
- **Лямбда-исчисление** - Алонзо Черч (1930-е)
- **Теория рекурсивных функций** - Клини, Гёдель
- **Математическая логика** - основа для ФП

### Первые языки (1950-1970)
- **LISP** - Джон Маккарти (1958) - первый функциональный язык
- **APL** - Кен Айверсон (1962) - массивно-ориентированный
- **ISWIM** - Питер Ландин (1966) - влияние на современные языки

### Развитие (1970-1990)
- **ML** - Робин Милнер (1973) - типизированный ФП
- **Miranda** - Дэвид Тернер (1985) - ленивые вычисления
- **Haskell** - комитет исследователей (1987-1990)

### Современность (1990-настоящее время)
- **Haskell 98/2010** - стандартизация языка
- **Erlang** - Джо Армстронг (1986) - конкурентность
- **Clojure** - Рич Хики (2007) - ФП на JVM
- **F#** - Microsoft (2005) - ФП на .NET
- **Scala** - Мартин Одерски (2004) - гибридный подход

---

## 🎯 Основные принципы ФП

### 1. Чистые функции (Pure Functions)
**Принцип**: Функция всегда возвращает одинаковый результат для одинаковых входных данных и не имеет побочных эффектов.

```haskell
-- Чистая функция
add :: Int -> Int -> Int
add x y = x + y

-- Нечистая функция (с побочным эффектом)
addWithPrint :: Int -> Int -> IO Int
addWithPrint x y = do
    putStrLn $ "Складываем " ++ show x ++ " и " ++ show y
    return (x + y)

-- Использование
main :: IO ()
main = do
    let result1 = add 5 3        -- Чистая функция
    result2 <- addWithPrint 5 3  -- Нечистая функция
    putStrLn $ "Результат: " ++ show result1
```

**Преимущества чистых функций**:
- Предсказуемость и тестируемость
- Возможность кэширования
- Параллельное выполнение
- Математическая корректность

### 2. Неизменяемость (Immutability)
**Принцип**: Данные не изменяются после создания, создаются новые структуры данных.

```haskell
-- Неизменяемые данные
data Person = Person
    { name :: String
    , age  :: Int
    } deriving (Show)

-- Создание нового объекта вместо изменения
updateAge :: Person -> Int -> Person
updateAge person newAge = person { age = newAge }

-- Использование
main :: IO ()
main = do
    let person1 = Person "Иван" 25
    let person2 = updateAge person1 26
    
    putStrLn $ "Исходный: " ++ show person1
    putStrLn $ "Обновленный: " ++ show person2
    putStrLn $ "Исходный не изменился: " ++ show person1
```

**Преимущества неизменяемости**:
- Безопасность в многопоточной среде
- Упрощение отладки
- Возможность оптимизации
- Математическая корректность

### 3. Функции высшего порядка (Higher-Order Functions)
**Принцип**: Функции могут принимать функции как аргументы и возвращать функции как результат.

```haskell
-- Функция высшего порядка
applyTwice :: (a -> a) -> a -> a
applyTwice f x = f (f x)

-- Функция, возвращающая функцию
multiplyBy :: Int -> (Int -> Int)
multiplyBy x = (* x)

-- Функция, принимающая функцию
filterList :: (a -> Bool) -> [a] -> [a]
filterList _ [] = []
filterList p (x:xs)
    | p x       = x : filterList p xs
    | otherwise = filterList p xs

-- Использование
main :: IO ()
main = do
    let double = (* 2)
    putStrLn $ "Применяем дважды: " ++ show (applyTwice double 5)
    
    let multiplyBy3 = multiplyBy 3
    putStrLn $ "Умножаем на 3: " ++ show (multiplyBy3 7)
    
    let numbers = [1..10]
    let evens = filterList even numbers
    putStrLn $ "Четные числа: " ++ show evens
```

### 4. Ленивые вычисления (Lazy Evaluation)
**Принцип**: Вычисления откладываются до момента, когда результат действительно нужен.

```haskell
-- Бесконечный список
infiniteList :: [Int]
infiniteList = [1..]

-- Берем только первые 5 элементов
takeFirst5 :: [Int]
takeFirst5 = take 5 infiniteList

-- Фильтрация бесконечного списка
evenNumbers :: [Int]
evenNumbers = filter even [1..]

-- Использование
main :: IO ()
main = do
    putStrLn $ "Первые 5: " ++ show takeFirst5
    putStrLn $ "Первые 5 четных: " ++ show (take 5 evenNumbers)
    
    -- Создаем бесконечный список, но используем только часть
    let squares = [x^2 | x <- [1..]]
    putStrLn $ "Первые 5 квадратов: " ++ show (take 5 squares)
```

### 5. Паттерн-матчинг (Pattern Matching)
**Принцип**: Сопоставление структуры данных с образцами для извлечения значений.

```haskell
-- Простой паттерн-матчинг
describeNumber :: Int -> String
describeNumber 0 = "Ноль"
describeNumber 1 = "Один"
describeNumber 2 = "Два"
describeNumber n
    | n < 0     = "Отрицательное число"
    | n < 10    = "Однозначное число"
    | n < 100   = "Двузначное число"
    | otherwise = "Большое число"

-- Паттерн-матчинг со списками
sumList :: [Int] -> Int
sumList [] = 0
sumList (x:xs) = x + sumList xs

-- Паттерн-матчинг с кортежами
processPair :: (Int, String) -> String
processPair (0, msg) = "Нулевое значение: " ++ msg
processPair (n, msg) = "Значение " ++ show n ++ ": " ++ msg

-- Использование
main :: IO ()
main = do
    putStrLn $ describeNumber 5
    putStrLn $ describeNumber (-3)
    putStrLn $ describeNumber 25
    
    let numbers = [1, 2, 3, 4, 5]
    putStrLn $ "Сумма: " ++ show (sumList numbers)
    
    putStrLn $ processPair (0, "тест")
    putStrLn $ processPair (42, "ответ")
```

---

## ✅ Преимущества ФП

### 1. **Математическая корректность**
- Функции соответствуют математическим функциям
- Возможность формальной верификации
- Доказательство корректности алгоритмов

### 2. **Безопасность в многопоточности**
- Отсутствие разделяемого состояния
- Иммутабельность данных
- Детерминированность результатов

### 3. **Модульность и переиспользование**
- Композиция функций
- Высокоуровневые абстракции
- Легкость тестирования

### 4. **Оптимизация**
- Ленивые вычисления
- Мемоизация
- Параллельное выполнение

### 5. **Читаемость кода**
- Декларативный стиль
- Отсутствие побочных эффектов
- Ясная структура данных

---

## ❌ Недостатки ФП

### 1. **Сложность обучения**
- Непривычная парадигма для императивных программистов
- Абстрактные концепции
- Сложность отладки

### 2. **Производительность**
- Накладные расходы на создание новых структур
- Сборка мусора
- Неэффективность для некоторых алгоритмов

### 3. **Ограничения**
- Сложность работы с состоянием
- Неэффективность для императивных задач
- Ограниченная поддержка в экосистеме

### 4. **Отладка**
- Сложность трассировки выполнения
- Абстрактность ошибок
- Неочевидность производительности

---

## 🔄 Сравнение с другими парадигмами

### ФП vs Императивное программирование

| Аспект | ФП | Императивное |
|--------|----|--------------|
| **Состояние** | Неизменяемое | Изменяемое |
| **Функции** | Первоклассные | Подпрограммы |
| **Данные** | Структуры данных | Переменные |
| **Побочные эффекты** | Минимизированы | Разрешены |
| **Параллелизм** | Простой | Сложный |

### ФП vs ООП

| Аспект | ФП | ООП |
|--------|----|-----|
| **Организация** | По функциям | По объектам |
| **Состояние** | Неизменяемое | Изменяемое |
| **Наследование** | Композиция | Наследование |
| **Полиморфизм** | Параметрический | Субтипирование |
| **Инкапсуляция** | Через модули | Через классы |

---

## 💻 Практические примеры на Haskell

### Пример 1: Работа со списками

```haskell
-- Базовые операции со списками
listOperations :: IO ()
listOperations = do
    let numbers = [1..10]
    let doubled = map (*2) numbers
    let filtered = filter even numbers
    let sumTotal = sum numbers
    let productTotal = product numbers
    
    putStrLn $ "Исходный список: " ++ show numbers
    putStrLn $ "Удвоенные числа: " ++ show doubled
    putStrLn $ "Четные числа: " ++ show filtered
    putStrLn $ "Сумма: " ++ show sumTotal
    putStrLn $ "Произведение: " ++ show productTotal
    
    -- Списковые включения
    let squares = [x^2 | x <- numbers, even x]
    putStrLn $ "Квадраты четных чисел: " ++ show squares
    
    -- Zipping списков
    let pairs = zip numbers (map (*2) numbers)
    putStrLn $ "Пары (число, удвоенное): " ++ show pairs

-- Рекурсивные функции
factorial :: Integer -> Integer
factorial 0 = 1
factorial n = n * factorial (n - 1)

fibonacci :: Integer -> Integer
fibonacci 0 = 0
fibonacci 1 = 1
fibonacci n = fibonacci (n - 1) + fibonacci (n - 2)

-- Использование
main :: IO ()
main = do
    listOperations
    
    putStrLn $ "Факториал 5: " ++ show (factorial 5)
    putStrLn $ "Числа Фибоначчи: " ++ show (map fibonacci [0..10])
```

### Пример 2: Работа с типами данных

```haskell
-- Пользовательские типы данных
data Shape = Circle Double
           | Rectangle Double Double
           | Triangle Double Double Double
           deriving (Show)

-- Функции для работы с фигурами
area :: Shape -> Double
area (Circle r) = pi * r * r
area (Rectangle w h) = w * h
area (Triangle a b c) = 
    let s = (a + b + c) / 2
    in sqrt (s * (s - a) * (s - b) * (s - c))

perimeter :: Shape -> Double
perimeter (Circle r) = 2 * pi * r
perimeter (Rectangle w h) = 2 * (w + h)
perimeter (Triangle a b c) = a + b + c

-- Тип для представления цвета
data Color = Red | Green | Blue | Yellow | Black | White
           deriving (Show, Eq)

-- Тип для фигуры с цветом
data ColoredShape = ColoredShape Shape Color
                  deriving (Show)

-- Функции для цветных фигур
getColor :: ColoredShape -> Color
getColor (ColoredShape _ color) = color

getShape :: ColoredShape -> Shape
getShape (ColoredShape shape _) = shape

-- Использование
main :: IO ()
main = do
    let circle = Circle 5.0
    let rectangle = Rectangle 4.0 6.0
    let triangle = Triangle 3.0 4.0 5.0
    
    putStrLn $ "Круг: " ++ show circle
    putStrLn $ "Площадь круга: " ++ show (area circle)
    putStrLn $ "Периметр круга: " ++ show (perimeter circle)
    
    putStrLn $ "Прямоугольник: " ++ show rectangle
    putStrLn $ "Площадь прямоугольника: " ++ show (area rectangle)
    
    let redCircle = ColoredShape circle Red
    putStrLn $ "Красный круг: " ++ show redCircle
    putStrLn $ "Цвет: " ++ show (getColor redCircle)
```

### Пример 3: Работа с функциями высшего порядка

```haskell
-- Функции высшего порядка
compose :: (b -> c) -> (a -> b) -> (a -> c)
compose f g = \x -> f (g x)

-- Каррирование и частичное применение
add :: Int -> Int -> Int
add x y = x + y

addFive :: Int -> Int
addFive = add 5

-- Функция для применения функции к каждому элементу
map' :: (a -> b) -> [a] -> [b]
map' _ [] = []
map' f (x:xs) = f x : map' f xs

-- Функция для фильтрации
filter' :: (a -> Bool) -> [a] -> [a]
filter' _ [] = []
filter' p (x:xs)
    | p x       = x : filter' p xs
    | otherwise = filter' p xs

-- Функция для свертки (fold)
foldl' :: (b -> a -> b) -> b -> [a] -> b
foldl' _ acc [] = acc
foldl' f acc (x:xs) = foldl' f (f acc x) xs

-- Использование
main :: IO ()
main = do
    let numbers = [1..10]
    
    -- Применяем различные функции
    let doubled = map' (*2) numbers
    let squared = map' (^2) numbers
    let evens = filter' even numbers
    let odds = filter' odd numbers
    
    putStrLn $ "Исходные числа: " ++ show numbers
    putStrLn $ "Удвоенные: " ++ show doubled
    putStrLn $ "В квадрате: " ++ show squared
    putStrLn $ "Четные: " ++ show evens
    putStrLn $ "Нечетные: " ++ show odds
    
    -- Частичное применение
    let addTen = add 10
    putStrLn $ "Добавляем 10 к 5: " ++ show (addTen 5)
    
    -- Композиция функций
    let doubleThenSquare = compose (^2) (*2)
    putStrLn $ "Удваиваем, затем возводим в квадрат 3: " ++ show (doubleThenSquare 3)
    
    -- Свертка
    let sumNumbers = foldl' (+) 0 numbers
    let productNumbers = foldl' (*) 1 numbers
    
    putStrLn $ "Сумма: " ++ show sumNumbers
    putStrLn $ "Произведение: " ++ show productNumbers
```

---

## 🧠 Вопросы для самопроверки

### Базовые вопросы
1. **Что такое чистая функция?**
   - Функция без побочных эффектов, всегда возвращающая одинаковый результат для одинаковых входных данных.

2. **В чем разница между функциональным и императивным программированием?**
   - ФП фокусируется на вычислениях через функции, ИП - на изменении состояния.

3. **Что означает неизменяемость в ФП?**
   - Данные не изменяются после создания, создаются новые структуры.

### Продвинутые вопросы
4. **Какие преимущества дает ленивое вычисление?**
   - Эффективность, возможность работы с бесконечными структурами, откладывание вычислений.

5. **Как работают функции высшего порядка?**
   - Принимают функции как аргументы и/или возвращают функции как результат.

6. **В чем преимущества паттерн-матчинга?**
   - Безопасное извлечение данных, декларативность, читаемость кода.

---

## 📖 Дополнительные материалы

### Книги
- **"Learn You a Haskell for Great Good!"** - Миран Липовача
- **"Real World Haskell"** - Брайан О'Салливан
- **"Programming in Haskell"** - Грэм Хаттон

### Онлайн ресурсы
- [Haskell Wiki](https://wiki.haskell.org/)
- [Haskell Documentation](https://www.haskell.org/documentation/)
- [Learn Haskell](https://learnyouahaskell.com/)

### Видео курсы
- **"Functional Programming in Haskell"** - edX
- **"Haskell для начинающих"** - YouTube
- **"Functional Programming Principles"** - Coursera

---

## 🎯 Заключение

Функциональное программирование представляет собой мощную парадигму, которая:

✅ **Обеспечивает математическую корректность** программ  
✅ **Упрощает многопоточное программирование**  
✅ **Повышает читаемость** и тестируемость кода  
✅ **Предоставляет мощные абстракции** для решения задач  

### Ключевые моменты для запоминания:
- **5 основных принципов**: Чистые функции, Неизменяемость, Функции высшего порядка, Ленивые вычисления, Паттерн-матчинг
- **Функция** - это математическое отображение входных данных в выходные
- **Данные** не изменяются, создаются новые структуры
- **ФП** подходит для математических задач и систем с высокой надежностью

### Следующая лекция:
**"Основные концепции ФП"** - углубленное изучение типов данных, функций и монад.

---

## 📝 Домашнее задание

1. **Создайте функцию `factorial`** с использованием рекурсии
2. **Реализуйте функцию `fibonacci`** для вычисления чисел Фибоначчи
3. **Создайте функцию `map'`** для работы со списками
4. **Добавьте функцию `filter'`** для фильтрации списков

**Критерии оценки**:
- Правильная реализация рекурсии (3 балла)
- Корректная работа с типами (3 балла)
- Читаемость и структурированность кода (2 балла)
- Дополнительная функциональность (2 балла)

**Максимальный балл: 10**
