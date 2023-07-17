# Regressions

#### Построение регрессий и базовая демонстрация градиентного спуска

При нажатии ЛКМ добавляется точка в декартовом пространстве (совпадает с координатами пикселей).
Серая кривая - "идеальная регрессия", построенная по формулам.
Черная кривая стремится к ней, изменяясь согласно градиентному спуску.
Клавиши-цифры позволяются переключаться между регрессиями.
Доступные регрессии:
1. Линейная регрессия $y = ax + b$
2. Квадратичная регрессия $y = ax^2 + bx + c$
3. Степенная регрессия $y = ax^b$
4. Показательная регрессия $y = ab^x$

Скрипт [solutions.py](solutions.py) использует Python библиотеку SymPy для подтверждения формул.

### Компиляция
Единственная зависимость - библиотека raylib
```console
$ g++ -std=c++17 main.cpp -lraylib -o main
$ ./main
```
`python3 solutions.py` выведет все используемые формулы.

### Вычисления
Для вычисления ошибки некоторой кривой $y = f(x)$ используется квадратичная ошибка - сумма квадратов разностей значения функции и $y$ точки из датасета:

$$E = \sum_{i = 0}^{n-1} (f(x_i) - y_i)^2$$

Для минимизации ошибки можно применить метод градиентного спуска. 
Это вовсе не является эффективным методом нахождения уравнения регрессии, однако является базовой демонстрацией одного из самых важных понятий машинного обучения.
Найдем частные производные ошибки относительно каждого параметра функции-регрессии.
Эти частные производные составляют градиентный вектор, показывающий направление возрастания ошибки. Рассмотрим на примере линейной регрессии.

**Уравнение регрессии**
$$y = ax + b$$
**Квадратичная ошибка**
$$E = \sum_{i = 0}^{n-1} (ax_i + b - y_i)^2$$
**Частные производные**
$$\frac{\partial E}{\partial a} = 2(ax_i + b - y_i)x$$

$$\frac{\partial E}{\partial b} = 2(ax_i + b - y_i)$$

**Градиентный вектор**
```math
\Delta E = [2(ax_i + b - y_i)x, 2(ax_i + b - y_i)]
```
Таким образом, для градиентного спуска необходимо сделать смещение на обратный вектор $-\Delta E$.
При этом "конечную" регрессию можно найти решив уравнение $-\Delta E = [0, 0]$, поскольку идеальной считается регрессия, для которой невозможно улучшить значение квадратичной ошибки.
Для линейной регрессии, поделив на 2 и распределя множители, получим:

$$\begin{cases}
\sum ax_i^2 + bx_i - y_ix_i = 0 \\ 
\sum ax_i + b - y_i = 0
\end{cases}
$$

Воспользуемся коммутативностью сложения:

$$\begin{cases}
\sum ax_i^2 + \sum bx_i - \sum y_ix_i = 0 \\ 
\sum ax_i + \sum b - \sum y_i = 0
\end{cases}
$$

И вынесем переменные:

$$\begin{cases}
a\sum x_i^2 + b\sum x_i - \sum y_ix_i = 0 \\ 
a\sum x_i + b\sum 1 - \sum y_i = 0
\end{cases}
$$

Дальше просто решим систему линейных уравнений и получим:

$$
a = \frac{n \sum x_iy_i - \sum x_i \sum y_i}{n \sum x_i^2 - (\sum x_i)^2}
$$

$$
b = \frac{\sum y_i - a\sum x_i }{n}
$$

Зная значения соответствующих сумм, можем получить оба параметра прямой $y = ax + b$.

---
Для нахождения формул степенной и показательной регрессий прологарифмируем уравнения.

$$
\begin{align} 
y & = ax^b \\
\ln y & = \ln(ax^b) = \ln a + b\ln x 
\end{align}
$$

Получаем линейное уравнение относительно $\ln a$ и $b$.

$$
\begin{align} 
y & = ab^x \\
\ln y & = \ln(ab^x) = \ln a + x\ln b 
\end{align}
$$

Получаем линейное уравнение относительно $\ln a$ и $\ln b$. 

Далее можно проделать те же операции, что и с линейной регрессией, но с новыми переменными. Останется лишь применить функцию $\exp(x)$ - возведение числа $e$ в степень $x$ для нахождения параметра по его натуральному логарифму.
