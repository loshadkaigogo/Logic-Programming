﻿# Отчет по лабораторной работе №1
## Работа со списками и реляционным представлением данных
## по курсу "Логическое программирование"

### студент: Меджидли М.И.

## Результат проверки

| Преподаватель     | Дата         |  Оценка       |
|-------------------|--------------|---------------|
| Сошников Д.В. |              |               |
| Левинская М.А.|              |               |

> *Комментарии проверяющих (обратите внимание, что более подробные комментарии возможны непосредственно в репозитории по тексту программы)*


## Введение

В языке программирования Prolog списки представляют собой голову и хвост (голова - один элемент и хвост - остальные), 
либо пустой элемент. Это позволяет рекурсивно обрабатывать список, что облегчает написания алгоритмов. 
Список является односвязным, так как мы можем идти только в одну сторону. В списке могут лежать элементы разных типов, 
так как Prolog не является статически типизированным языком как C, C++.
 
Доступ к элементу в Prolog осуществляется за линейное время, а добавление элемента за константное. Это очень напоминает
структуру данных list, которая есть в С++, но там list двухсвязный. 

## Задание 1.1: Предикат обработки списка

`list_next(Elem , List, NextElem)` - предикат нахождение элемента списка, следующего за данным

Примеры использования:
```prolog
?- list_next(2, [1,2,3,4], X).
X = 3 ;
false.
?- list_next(X, [1,2,3,4], Y).
X = 1,
Y = 2 ;
X = 2,
Y = 3 ;
X = 3,
Y = 4 ;
false.
```

Реализация:
```prolog
list_next(Elem , List, NextElem):- % 1 способ
   list_append(_,[Elem, NextElem|_], List).

list_next_s(Elem, List, NextElem):- % 2 способ
   append(_, [Elem,NextElem|_], List).
```

Для каждого вхождения проверяем, является ли конкатенация первого и второго, у которого Elem и NextElem идут друг за другом,
списком List.  


`list_next_el(List, Elem, NextElem)` - предикат нахождение элемента списка, следующего за данным

Примеры использования:
```prolog
?- list_next_el([1,2,3,4,5,6], X, Y).
X = 1,
Y = 2 ;
X = 2,
Y = 3 ;
X = 3,
Y = 4 ;
X = 4,
Y = 5 ;
X = 5,
Y = 6 ;
false.
```

Реализация:
```prolog
                               % 3 способ
list_next_el([A,B|_], A, B).
list_next_el([_|T], A, B):- list_next_el(T, A, B).
```

С помощью рекурсии идём по списку и находим 2 элемента, стоящие рядом.
 

## Задание 1.2: Предикат обработки числового списка

`parity_partition(List, List_even, List_uneven)` - предикат разделения списка на два по принципу четности элементов

Примеры использования:
```prolog
?- parity_partition([1,2,3,4,5,6], EVEN, UNEVEN).
EVEN = [2, 4, 6],
UNEVEN = [1, 3, 5] ;
false.
?- parity_partition([1,2,3,4,5,6], [X, Y, Z], UNEVEN).
X = 2,
Y = 4,
Z = 6,
UNEVEN = [1, 3, 5] ;
false.
```

Реализация:
```prolog
parity_partition([],[],[]).  % 1 способ
parity_partition([A|B], [A|X], Y):- 0 is A mod 2, parity_partition(B,X,Y).
parity_partition([A|B], X, [A|Y]):- 1 is A mod 2, parity_partition(B,X,Y).

:- use_module(library(clpfd)). % 2 способ
list_evens_odds([], [], []).
list_evens_odds([E|Zs], [E|Es], Os) :- 0 #= E mod 2, list_evens_odds(Zs, Es, Os).
list_evens_odds([E|Zs], Es, [E|Os]) :- 1 #= E mod 2, list_evens_odds(Zs, Es, Os).
```

Как обычно используем рекурсивность списков в Prolog. Если элемент из исходного списка делится на 2 без остатка, тогда добавляем
его в первый список, иначе во второй. 2-ой способ аналогичен первому, только использует библиотеку clpfd. 

## Задание 1.3: Совместное использование

Удаление следующего элемента за заданным:
```prolog
remove_next_elem(A, Elem, X) :- list_next(Elem, A, Next_Elem), list_remove(Next_Elem, A, X).
```

Пример использования:
```prolog
?- remove_next_elem([1,2,3,4], 1, X).
X = [1, 3, 4] ;
false.
```

## Задание 2: Реляционное представление данных

Реляционное представление данных - это представление каких-то записей или объектов, которые имеют связи друг с другом (relativity).
Широко применяется в БД, где одна таблица связана с другой, что делает удобным хранение и получение данных. Но также есть и недостатки
реляционного представления данных. При запросе к элементам таких БД получаем множество удовлетворяющих элементов внутренней структуре, 
что в некоторых ситуациях сильно усложняет обработку. Так приходится следить за тем, чтобы итоговые предикаты обработки полностью 
охватывали заданное множество. Например, предикаты, вычисляющие списки, не имели повторений, либо, наоборот - были нужной длины 
и включали все необходимые вхождения. 

Преимущества представления в one.pl:

1) Получение оценки студента за экзамен
2) Легко узнать номер группы студента

Недостатки представления в one.pl:

1) По сравнению с four.pl, для пользователя неудобно то, что нельзя по номеру группы получить список группы, а для предмета - оценки.
2) По сравнени. с three.pl неудобно то, что для студента нельзя получить список его оценок.

`res_in_group(Group, Table, Avg_Mark)` - получить таблицу групп и средний балл по каждой из групп

Пример использования:
```prolog
?- res_in_group(Group,Table,Avg_Mark). 
Group = 101,
Table = ['Петровский', 'Сидоров', 'Мышин', 'Безумников', 'Густобуквенникова'],
Avg_Mark = 3.9 ;
Group = 102,
Table = ['Петров', 'Ивановский', 'Биткоинов', 'Шарпин', 'Эксель', 'Текстописов', 'Криптовалютников', 'Азурин', 'Круглотличников'],
Avg_Mark = 3.7777777777777777 ;
Group = 103,
Table = ['Сидоркин', 'Эфиркина', 'Сиплюсплюсов', 'Программиро', 'Клавиатурникова', 'Решетников', 'Текстописова', 'Вебсервисов'],
Avg_Mark = 3.7708333333333335 ;
Group = 104,
Table = ['Иванов', 'Запорожцев', 'Джаво', 'Фулл', 'Круглосчиталкин', 'Блокчейнис'],
Avg_Mark = 3.861111111111111.
```

Реализация:
```prolog
sum_list([], 0, C) :- C is 0.
sum_list([H|T], Sum, N) :- sum_list(T, Rest, C), Sum is H + Rest, N is C+1.
avg_mark([H|_], ANS) :- findall(Z, grade(H, _, Z), R), sum_list(R, Sum, N), ANS is Sum/N.
all_marks([], RES, N) :- RES is 0, N is 0. 
all_marks([H|T], RES, N) :- avg_mark([H], ANS), all_marks(T, Y, K), N is K+1, RES is Y+ANS.
res_in_group(X, L, R) :- bagof(Y, student(X, Y), L), all_marks(L, RES, N), R is RES/N.
```

При помощи bagof, получаем для каждого номера группы список имён. При помощи предиката all_marks([H|T], RES, N)), который рекурсивно 
считает сумму и количество оценок для списка людей, внутри себя он использует предикат avg_mark([H|_], ANS), который подсчитает средную 
оценку для каждого человека.

`bad_mark(Subject, Names)` - для каждого предмета получить список студентов, не сдавших экзамен (grade=2)

Пример использования:
```prolog
?- bad_mark(Subject, Names).
Subject = 'ENG',
Names = ['Эфиркина'] ;
Subject = 'FP',
Names = ['Криптовалютников'] ;
Subject = 'INF',
Names = ['Эфиркина', 'Джаво', 'Безумников'] ;
Subject = 'LP',
Names = ['Запорожцев', 'Эфиркина', 'Текстописов'] ;
Subject = 'MTH',
Names = ['Запорожцев', 'Круглосчиталкин', 'Густобуквенникова', 'Криптовалютников', 'Блокчейнис', 'Азурин'] ;
Subject = 'PSY',
Names = ['Биткоинов', 'Текстописова', 'Криптовалютников', 'Азурин', 'Вебсервисов'].
```

Реализация:
```prolog
bad_mark(S, N) :- bagof(Z, grade(Z, S, 2), N).
```

Для каждого предмета выводим людей получивших оценку 2, при помощи bagof.

`count_mark_2(Group, Count)` - найти количество не сдавших студентов в каждой из групп.

Пример использования:
```prolog
?- count_mark_2(Group, Count). 
Group = 101,
Count = 2 ;
Group = 102,
Count = 4 ;
Group = 103,
Count = 3 ;
Group = 104,
Count = 4.
```

Реализация:
```prolog
count_mark_2_in_group([], C) :- C is 0.
count_mark_2_in_group([H|T], C) :- grade(H, _, 2) -> count_mark_2_in_group(T, N), C is N+1; count_mark_2_in_group(T, N), C is N.
count_mark_2(X, C) :- bagof(Y, student(X, Y), L), count_mark_2_in_group(L, C).
```

Для каждой группы выводит количество людей, получивших оценки 2, список людей для каждой группы определяется при помощи bagof.
Потом вызывается предикат count_mark_2_in_group([H|T], C), который рекурсивно для списка людей считает количество людей, получивших оценку 2.


## Выводы

Данная лабораторная работа научила меня работать с языком программирования Prolog, понять, как устроен этот язык и какую роль в нём играет рекурсия.
Этот язык сложно применить в современном мире для решения каких-либо задач, но всё же Prolog является представителем парадигмы
логического программмирования, а эти знания будут полезны для любого программмиста. Чтобы писать на Prolog, нужно думать
по-другому. Таким образом Prolog развивает мышление, тем самым улучшает программиста, как специалиста.




