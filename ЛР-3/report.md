---
# Front matter
title: "Отчёт по лабораторной работе №3"
subtitle: "Шифр гаммирования"
author: "Бакундукизе Эжид Принц НФИмд-01-21"

# Generic otions
lang: ru-RU
toc-title: "Содержание"

# Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

# Pdf output format
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
### Fonts
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Misc options
indent: true
header-includes:
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Изучение алгоритма шифрования гаммированием.

# Теоретические сведения

## Шифр гаммирования

Гаммирование — метод симметричного шифрования, заключающийся в «наложении» последовательности, состоящей из случайных чисел, на открытый текст. Последовательность случайных чисел называется гамма-последовательностью и используется для зашифровывания и расшифровывания данных [1]. 

Схема шифрования гаммированием представлена на рис.1.

![Схема шифрования гаммированием](image/1.png){ #fig:001 }

Гаммирование - процедура наложения при помощи некоторой функции F на исходный текст гаммы шифра, т.е. псевдослучайной последовательности выходов генератора G. Псевдослучайная последовательность по своим статистическим свойствам неотличима от случайной последовательности, но является детерминированной, т.е. известен алгоритм ее формирования. Обчно в качестве функции F берется операция поразрядного сложения по модулю два или по модулю N (N - число букв алфавита открытого текста).

Простейший генератор пседвослучайной последовательности можно представить рекуррентным соотношением (рис.2):

![Простейший генератор псевдослучайной последовательности](image/2.png){ #fig:002 }

Полученный зашифрованный текст является достаточно трудным для раскрытия в том случае, если гамма шифра не содержит повторяющихся битовых последовательностей и изменяется случайным образом для каждого шифруемого слова. Если период гаммы превышает длину всего зашифрованного текста и неизвестна никакая часть исходного текста, то шифр можно раскрыть только прямым перебором (подбором ключа) [2].

Стойкость шифров, основанных на процедуре гаммирования, зависит от характеристик гаммы - длины и равномерности распределения вероятностей появления знаков гаммы. При использовании генератора псевдослучайных последовательностей получаем бесконечную гамму. Однако, возможен режим шифрования конеxной гаммы. В роли конечной гаммы выступать фраза.

Метод гаммирования становится бессильным, если известен фрагмент исходного текста и соответствующая ему шифрограмма. В этом случае простым вычитанием по модулю 2 получается отрезок псевдослучайной последовательности и по нему восстанавливается вся эта последовательность.


# Выполнение работы

## Реализация алгоритма шифрования гаммирования конечной гаммой на Python

```
def gamma():
    #создаем алфавит
    dict = {"а" :1, "б" :2 , "в" :3 ,"г" :4 ,"д" :5 ,"е" :6,"ж": 7, "з": 8, "и": 9, "й": 10, "к": 11, "л": 12,
            "м": 13, "н": 14, "о": 15, "п": 16,
            "р": 17, "с": 18, "т": 19, "у": 20, "ф": 21, "х": 22, "ц": 23, "ч": 24, "ш": 25, "щ": 26, "ъ": 27,
            "ы": 28, "ь": 29, "э": 30, "ю": 31, "я": 32
            }
    
    # меняем местами ключ и значение, такой словарь понадобится для расшифровки
    dict2 = {v: k for k, v in dict.items()}
    
    g = input("Гамма: ").lower()
    text = input("Текст для шифрования: ").lower()
    
    list_text = list() #числа букв из текста
    list_gamma = list() #числа букв для гаммы
    
    #запишем числа в список
    for i in text:
        list_text.append(dict[i])
    print("Числа текста: ", list_text)
    
    #запишем числа для гаммы
    
    for i in g:
        list_gamma.append(dict[i])
    print("числа гаммы: ", list_gamma)
    
    result = list() #сюда будем записывать результат
    
    ch = 0
    for i in text:
        try:
            a = dict[i] + list_gamma[ch]
        except:
            ch=0
            a = dict[i] + list_gamma[ch]
        if a >=33:
            a = a%33
        ch+=1
        result.append(a)
        
    print("Числа зашифрованного текста: ", result)
    
    # теперь обратно числа представим в виде букв
    text2=""
    for i in result:
        text2 += dict2[i]
    print("Зашифрованный текст: ", text2)
    
    #Дешифровка
    
    list_text2 = list()
    
    for i in text2:
        list_text2.append(dict[i])
        
    ch = 0
    list_text2_2 = list()
    for i in list_text2:
        try:
            a = i - list_gamma[ch]
        except:
            ch=0
            a = i - list_gamma[ch]
        if a < 1:
            a = 33 + a
            
        list_text2_2.append(a)
        ch+=1
        
    text_decrypted = ""
    for i in list_text2_2:
        text_decrypted+=dict2[i]
        
    print("Дешифровка: ", text_decrypted)
```

## Контрольный пример

![Работа алгоритма гаммирования](image/01.png){ #fig:003 width=70% height=70%}

# Выводы

В ходе выполнения данной лабораторной работы мы познакомились с алгоритмом шифрования гаммирования. В рамках задания программно были реализованы алгоритмы шифрования и дешифрования гаммированием конечной гаммы. 

# Список литературы{.unnumbered}

1. [Шифрование методом гаммирования](http://altaev-aa.narod.ru/security/XOR.html)
2. [Режим гаммирования в блочном алгоритме шифрования](https://kabinfo.ucoz.ru/index/shifr_reshetka_kardano/0-374)