---
# Front matter
title: "Отчет по лабораторной работе №3"
subtitle: "Шифрование гаммированием"
author: "Бурдина Ксения Павловна"
institute: Российский университет дружбы народов, Москва, Россия
date: 13 октября 2023

# Generic otions
lang: ru-RU
toc-title: "Содержание"

# Pdf output format
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
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

Целью данной работы является освоение шифрования гаммированием, а также его программная реализация.

# Задание

1. Изучить способ шифрования гаммированием.
2. Реализовать алгоритм шифрования гаммированием конечной гаммой.

# Теоретическое введение

Из всех схем шифрования простейшей и наиболее надежной является схема однократного использования:

![Схема однократного использования](screens/1.jpg){width=80%}

Формируется $m-$ разрядная случайная двоичная последовательность - ключ шифра. Отправитель производит побитовое сложение по модулю два ($mod 2$) ключа $k=k_1 k_2 ... k_i ... k_m$ и $m-$ разрядной двоичной последовательности $p=p_1 p_2 ... p_i ... p_m$, соответствующей посылаемому сообщению:

$$c_i = p_i \oplus k_i, i =\overline{1,m}$$

где $p_i$ - $i-$й бит исходного текста, $k_i$ - $i-$й бит ключа, $\oplus$ - операция побитового сложения (XOR), $c_i$ - $i-$й бит получившейся криптограммы: $c=c_1 c_2 ... c_i ... c_m$.

Операция побитного сложения является обратимой, то есть $(x \oplus y) \oplus y = x$, поэтому дешифрование осуществляется повторным применением операции $\oplus$ к криптограмме:
$$p_i = c_i \oplus k_i, i = \overline{1,m}$$

Основным недостатком такой схемы является равенство объема ключевой информации и суммарного объема передаваемых сообщений. Данный недостаток можно убрать, использовав ключ в качестве "зародыша", порождающего значительно более длинную ключевую последовательность.

На данном рисунке представлена такая схема, которая и называется гаммированием:

![Шифрование гаммированием](screens/2.jpg){width=80%}

Гаммирование - процедура наложения при помощи некоторой функции $F$ на исходный текст гаммы шифра, то есть псевдослучайной последовательности (ПСП) с выходом генератора $G$. Псевдослучайная последовательность по своим статистическим свойствам неотличима от случайной последовательности, но является детерминированной, то есть известен алгоритм ее формирования. Обычно в качестве функции $F$ берется операция поразрядного сложения по модулю два или по модулю $N$ ($N$ - число букв алфавита открытого текста) [[1]](https://esystem.rudn.ru/pluginfile.php/2089869/mod_folder/content/0/%D0%A2%D1%80%D0%B0%D0%B4%D0%B8%D1%86%D0%B8%D0%BE%D0%BD%D0%BD%D1%8B%D0%B5%20%D1%88%D0%B8%D1%84%D1%80%D1%8B%20%D1%81%20%D1%81%D0%B8%D0%BC%D0%BC%D0%B5%D1%82%D1%80%D0%B8%D1%87%D0%BD%D1%8B%D0%BC%20%D0%BA%D0%BB%D1%8E%D1%87%D0%BE%D0%BC.pdf?forcedownload=1).

Простейший генератор псевдослуайной последовательности можно представить рекуррентным соотношением:
$$\gamma _i = a*\gamma _{i-1} + b*mod(m), i = \overline{1,m}$$
где $\gamma _i$ - i-й член последовательности псевдослучайных чисел, $a,\gamma _0, b$ - ключевые параметры. Такая последовательность состоит из целых чисел от 0 до m-1. Если элементы $\gamma _i$ и $\gamma _j$ совпадут, то совпадут и последующие участки: $\gamma _{i+1} =\gamma _{j+1}, \gamma _{i+2}=\gamma _{j+2}$. Таким образом, ПСП является периодической. Знание периода гаммы существенно облегчает криптоанализ. Максимальная длина периода равна m. Для ее достижения необходимо удовлетворить следующим условиям:

- b и m - взаимно простые числа;
- a-1 делится на любой простой делитель числа m;
- a-1 кратно 4, если m кратно 4.

Стойкость шифров, основанных на процедуре гаммрования, зависит от характеристик гаммы - длины и равномерности распределения вероятностей появления знаков гаммы [[2]](https://esystem.rudn.ru/pluginfile.php/2089880/mod_folder/content/0/lab03.pdf?forcedownload=1).

При использовании генератора ПСП получаем бесконечную гамму. Однако, возможен режим шифрования конечной гаммы. В роли конечной гаммы может выступать фраза. Как и ранее, используется алфавитный порядок букв, то есть буква "а" имеет порядквый номер 1, "б" - 2 и так далее.

Например, зашифруем слово "ПРИКАЗ" ("16 17 09 11 01 08") гаммой "ГАММА" ("04 01 13 13 01"). Будем использовать операцию побитового сложения по модулю 33 (mod 33). Получаем криптограмму: "УСХЧБЛ" ("20 18 22 24 02 12").

# Ход выполнения лабораторной работы

Для реализации шифров перестановки будем использовать среду JupyterLab. Выполним необходимую задачу.

1. Зададим функцию определения алфавита для дальнейшей работы с шифрами перестановки:

![Алфавит для реализации шифров](screens/3.jpg){width=80%}

2. Пропишем функцию, в которой запишем принцип формирования алгоритма конечной гаммой:

![Функция алгоритма шифрования конечной гаммой](screens/4.jpg){width=80%}

Здесь мы подаем на ввод некоторый текст, определяем его алфавит и выводим на экран. Далее определяем длину исходного текста. Задаем функцию для нахождения индексов каждого символа в заданном сообщении. После этого работаем с командами ввода сообщения и преобразования его с помощью конечной гаммы, находим индексы символов заданной гаммы и накладываем шифр заданной гаммой поверх исходного текста.

Далее формируем зашифрованный текст с помощью схемы гаммирования, то есть складываем индексы побитно и получаем индексы для шифровки. Выводим на экран полученный шифр в виде символов, а также их индексацию.

3. Создаем функцию для принятия вводимого текста и его преобразования. Для этого передаем в функцию значения исходного текста и гаммы, которая будет накладываться на текст.
После этого вводим проверочные данные и вызываем функцию для работы алгоритма:

![Реализация шифрования гаммированием на примере](screens/5.jpg){width=80%}

Видим, что полученное зашифрованное сообщение получилось аналогичным с примером, соответственно, шифрование выполнено верно.

4. Для проверки корректной работы алгоритма введем еще несколько данных: 

![Результат шифрования гаммированием на русском](screens/6.jpg){width=80%}

![Результат шифрования гаммированием на английском](screens/7.jpg){width=80%}

Если посмотреть по индексам символов, видно, что алгоритм работает корректно, значит, шифрование гаммированием конечной гаммой реализовано верно.

# Листинг программы

    import numpy as np

    def get_alph(option):
      if option=='eng':
        return(list(map(chr, range(ord('a'), ord('z')+1))))
      elif option=='rus':
        return(list(map(chr, range(ord('a'), ord('я')+1))))
    
    def gamma_encrypt(message: str, gamma: str):
      alph = get_alph('eng')
      if message.lower() not in alph:
        alph = get_alph('rus')
      print(alph)
      m = len(alph)
      def encrypt(letters_pair: tuple):
        idx = (letters_pair[0]+1) + (letters_pair[1]+1) % m
        if idx > m:
          idx = idx-m
        return idx-1
      message_clear = list(filter(lambda s: s.lower() in alph, message))
      gamma_clear = list(filter(lambda s: s.lower() in alph, gamma))
      message_ind = list(map(lambda s: alph.index(s.lower()), message_clear))
      gamma_ind = list(map(lambda s: alph.index(s.lower()), gamma_clear))
      for i in range(len(message_ind) - len(gamma_ind)):
        gamma_ind.append(gamma_ind[i])
      print(f'{message.upper()} -> {message_ind}\n{gamma.upper()} -> {gamma_ind}')
      encrypted_ind = list(map(lambda s: encrypt(s), zip(message_ind, gamma_ind)))
      print(f'encrypted form: {encrypted_ind}\n')
      return ''.join(list(map(lambda s: alph[s], encrypted_ind))).upper()
    
    def test_encryption(message: str, gamma: str):
      print(f'encryption result: {gamma_encrypt(message, gamma)}')
    
    message = 'ПРИКАЗ'
    gamma = 'ГАММА'
    test_encryption(message, gamma)

    message = 'ЗДЕСЬ МОГЛА БЫ БЫТЬ ВАША РЕКЛАМА'
    gamma = 'КОЛИЗЕЙ'
    test_encryption(message, gamma)

    message = 'FEEL THE RUSSIAN STYLE'
    gamma = 'HATTERS'
    test_encryption(message, gamma)

# Выводы

В ходе работы мы изучили и реализовали алгоритм шифрования гаммированием конечной гаммой.

# Список литературы

1. Традиционные шифры с симметричным ключом [[1]](https://esystem.rudn.ru/pluginfile.php/2089869/mod_folder/content/0/%D0%A2%D1%80%D0%B0%D0%B4%D0%B8%D1%86%D0%B8%D0%BE%D0%BD%D0%BD%D1%8B%D0%B5%20%D1%88%D0%B8%D1%84%D1%80%D1%8B%20%D1%81%20%D1%81%D0%B8%D0%BC%D0%BC%D0%B5%D1%82%D1%80%D0%B8%D1%87%D0%BD%D1%8B%D0%BC%20%D0%BA%D0%BB%D1%8E%D1%87%D0%BE%D0%BC.pdf?forcedownload=1)

2. Методические материалы курса [[2]](https://esystem.rudn.ru/pluginfile.php/2089880/mod_folder/content/0/lab03.pdf?forcedownload=1)

