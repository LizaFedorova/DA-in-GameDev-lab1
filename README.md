# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Федорова Елизавета Евгеньевна
- РИ210932

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.

## Цель работы
Ознакомиться с основными операторами зыка Python на примере реализации линейной регрессии.

## Задание 1
### Написать программы Hello World на Python и Unity.
- Для Python в отчете привести скриншоты с демонстрацией сохранения документа google.colab на свой диск с запуском программы, выводящей сообщение Hello World.

![image_1](https://user-images.githubusercontent.com/103308669/192159413-dd630c0a-9d52-4e8e-9086-41d1629a25a4.png)

![image](https://user-images.githubusercontent.com/103308669/192158003-a242c358-0a70-4c68-9567-2d6d8ee73221.png)

- Для Unity в отчете привести скриншот вывода сообщения Hello World в консоль.

![image](https://user-images.githubusercontent.com/103308669/192158021-035fbda7-2e8f-415c-8ff1-1d0aecd825c1.png)

![image](https://user-images.githubusercontent.com/103308669/192158024-ce2f94fd-34d7-42f2-a15f-8b4d7a9236b3.png)

## Задание 2
### Пошагово выполнить каждый пункт раздела "ход работы" с описанием и примерами реализации задач.

Ход работы:
- Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.

```py
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21 ,22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x,y)
```

![image](https://user-images.githubusercontent.com/103308669/192158073-d5cb3ed5-3b1f-41b7-a476-c234ac721a51.png)

- Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.

```py
def model(a, b, x):
  return a * x + b
def loss_function(a, b, x, y):
  num = len(x)
  prediction = model (a,b,x)
  return(0.5/num) * (np.square(prediction - y)).sum()
def optimize(a, b, x, y):
  num = len(x)
  prediction = model (a,b,x)
  da = (1.0/num)*((prediction - y)*x).sum()
  db = (1.0/num)*((prediction - y).sum())
  a = a - Lr*da
  b = b - Lr*db
  return a,b
def iterate(a, b, x, y, times):
  for i in range(times):
    a,b = optimize(a, b, x, y)
  return a,b
```

![image](https://user-images.githubusercontent.com/103308669/192158097-d28d5220-2f48-4a22-b13d-f635bd9ccea0.png)

- Начать итерацию.

![image](https://user-images.githubusercontent.com/103308669/192158109-f79ad15a-913a-4e36-9b05-b00823d8217f.png)

Шаг 1. Инициализация и модель итеративной оптимизации.

```py
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

a,b = iterate(a,b,x,y,1)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```
![image](https://user-images.githubusercontent.com/103308669/192158147-afed4867-eebf-4086-ae81-8879a484fc7b.png)

Шаг 2.  На второй итерации отображаются значения параметров, значения потерь и эффекты визуализации после итерации.

```py
a,b = iterate(a,b,x,y,2)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image](https://user-images.githubusercontent.com/103308669/192158171-dab6ecfc-a3a0-4f61-a567-c6edf2b59081.png)

Шаг 3. Третья итерация показывает значения параметров, значения потерь и визуализацию после итерации.

```py
a,b = iterate(a,b,x,y,3)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image](https://user-images.githubusercontent.com/103308669/192158203-0ffaff03-da4c-440b-9915-8411bc724cc0.png)

Шаг 4. На четвертой итерации отображаются значения параметров, значения потерь и эффекты визуализации.

```py
a,b = iterate(a,b,x,y,4)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image](https://user-images.githubusercontent.com/103308669/192158222-7e6b20cc-a1d0-4c39-b2f0-e7d5dda3f7ca.png)

Шаг 5. Пятая итерация показывает значение параметра, значение потерь и эффект визуализации после итерации.

```py
a,b = iterate(a,b,x,y,5)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image](https://user-images.githubusercontent.com/103308669/192158240-f47575ae-b6a4-484c-a3ea-302ae5817d91.png)

Шаг 6. 10000-я итерация, показывающая занчения параметров, потери и визуализацию после итерации.

```py
a,b = iterate(a,b,x,y,10000)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image](https://user-images.githubusercontent.com/103308669/192158256-145e4d0e-392b-4dda-b76c-75777d8484d5.png)

## Задание 3
### Изучить код на Python и ответить на вопросы:

```py
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21 ,22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x,y)

def model(a, b, x):
  return a * x + b
def loss_function(a, b, x, y):
  num = len(x)
  prediction = model (a,b,x)
  return(0.5/num) * (np.square(prediction - y)).sum()
def optimize(a, b, x, y):
  num = len(x)
  prediction = model (a,b,x)
  da = (1.0/num)*((prediction - y)*x).sum()
  db = (1.0/num)*((prediction - y).sum())
  a = a - Lr*da
  b = b - Lr*db
  return a,b
def iterate(a, b, x, y, times):
  for i in range(times):
    a,b = optimize(a, b, x, y)
  return a,b

a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

a,b = iterate(a,b,x,y,1)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

- Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.

Loss может стремиться к нулю, если значения x и y стремятся к нулю и b = 0.

```py
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

a,b = iterate(a,b,x,y,10000)
prediction = model (a,b,x)
loss = loss_function(a,0,x*10**(-30),y*10**(-30))
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image](https://user-images.githubusercontent.com/103308669/192158297-435d417a-af32-4bb6-99dd-b3efd1dbc256.png)

- Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.

Параметр Lr определяет точность графика. При увеличении параметра Lr, точки на графике выравниваются по оси ОХ. Так же этот параметр изменяет значения переменных a, b, loss. 

```py
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.1

a,b = iterate(a,b,x,y,1)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image](https://user-images.githubusercontent.com/103308669/192158313-9a550b1f-64ad-4f3b-8a83-cca2bc3da77c.png)

## Выводы

В ходе выполнения данной лабораторной работы, я настроила Unity, Visual Studio и Jupyter Notebook для дальнейшей работы с ними. Также составила простейшие программы на Unity и Python. Ознакомилась с основными операторами языка Python на примере реализации линейной регрессии и научилась анализировать работу программы.
