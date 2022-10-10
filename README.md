# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
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

![image_2](https://user-images.githubusercontent.com/103308669/192161164-5d7370a1-7d1c-4b78-ab5c-7913162db4c0.png)

- Для Unity в отчете привести скриншот вывода сообщения Hello World в консоль.
(Я использовала юнити версии 2022 года)

![image_3](https://user-images.githubusercontent.com/103308669/192160340-b5c115fb-cb45-45f8-9d84-d15b51c1893f.png)

![image_4](https://user-images.githubusercontent.com/103308669/192160351-849db01b-bf08-4bee-93a0-3d7f01b3725b.png)

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

![image_5](https://user-images.githubusercontent.com/103308669/192160365-97175c22-b8dd-4f8a-b12c-3227b808381a.png)

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

![image_6](https://user-images.githubusercontent.com/103308669/192160382-e443c117-92a2-4cc2-bd9f-7c3aa8baa1c1.png)

- Начать итерацию.

![image_7](https://user-images.githubusercontent.com/103308669/192160395-f9c801af-e7d9-41c4-8f17-7ecc5083c7b5.png)

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
![image_8](https://user-images.githubusercontent.com/103308669/192160402-818e357c-e816-45a9-bdcc-722b1298e7e9.png)

Шаг 2.  На второй итерации отображаются значения параметров, значения потерь и эффекты визуализации после итерации.

```py
a,b = iterate(a,b,x,y,2)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image_9](https://user-images.githubusercontent.com/103308669/192160408-6cef4afa-56a7-49a6-ac34-49353e39ce95.png)

Шаг 3. Третья итерация показывает значения параметров, значения потерь и визуализацию после итерации.

```py
a,b = iterate(a,b,x,y,3)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image_10](https://user-images.githubusercontent.com/103308669/192160421-224af954-294d-4070-9058-e6a26b8d0f1c.png)

Шаг 4. На четвертой итерации отображаются значения параметров, значения потерь и эффекты визуализации.

```py
a,b = iterate(a,b,x,y,4)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image_11](https://user-images.githubusercontent.com/103308669/192160433-93eefc00-5124-4404-b354-799e49d4cb1b.png)

Шаг 5. Пятая итерация показывает значение параметра, значение потерь и эффект визуализации после итерации.

```py
a,b = iterate(a,b,x,y,5)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image_12](https://user-images.githubusercontent.com/103308669/192160442-24fe996d-900e-4142-9be2-3f5d84639188.png)

Шаг 6. 10000-я итерация, показывающая занчения параметров, потери и визуализацию после итерации.

```py
a,b = iterate(a,b,x,y,10000)
prediction = model (a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)
```

![image_13](https://user-images.githubusercontent.com/103308669/192160450-50523846-a674-4939-bf9b-ab750f61babc.png)

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

![image_14](https://user-images.githubusercontent.com/103308669/192160468-228f1632-7203-4212-bdca-4f23a6116b2f.png)

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

![image_15](https://user-images.githubusercontent.com/103308669/192160474-f3df0908-8805-4c85-8dec-cbd7dec6ad0c.png)

## Выводы

В ходе выполнения данной лабораторной работы, я настроила Unity, Visual Studio и Jupyter Notebook для дальнейшей работы с ними. Также составила простейшие программы на Unity и Python. Ознакомилась с основными операторами языка Python на примере реализации линейной регрессии и научилась анализировать работу программы.
