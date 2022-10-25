# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
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
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Постановка задачи.
В данной лабораторной работе создается ML-агент и тренируется нейросеть, задача которой будет заключаться в управлении шаром. Задача шара заключается в том, чтобы оставаясь на плоскости находить кубик, смещающийся в заданном случайном диапазоне координат.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity. 
-Создала новый пустой 3D проект на Unity.
![image_1](https://user-images.githubusercontent.com/103308669/197757325-717b3333-0e26-4f82-8baf-460fbcbc2bb1.png)

-Скачала папку с ML агентом. В созданный проект добавила ML Agent, выбрав Window - Package Manager - Add Package from disk. Последовательно добавила .json – файлы:
		ml-agents-release_19 / com,unity.ml-agents / package.json
		ml-agents-release_19 / com,unity.ml-agents.extensions / package.json
![image_2](https://user-images.githubusercontent.com/103308669/197757513-854f225b-c4c1-4c2f-97fa-c0cefa7867c6.png)

-Во вкладке с компонентами (Components) внутри Unity появилась строка ML Agent.
-Далее запустила Anaconda Prompt для возможности запуска команд через консоль.
-Написала серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек:
	mlagents 0.28.0;
	torch 1.7.1;
![image_3](https://user-images.githubusercontent.com/103308669/197757916-340a777d-c78b-42b1-9b5e-7a4e19ad0dcc.png)
![image_4](https://user-images.githubusercontent.com/103308669/197757973-815ae747-7a25-40c8-9b08-7e7ca2e5d957.png)

-Создала на сцене плоскость, куб и сферу так, как показано на рисунке ниже. Создала простой C# скрипт-файл и подключила его к сфере.
![image_5](https://user-images.githubusercontent.com/103308669/197758100-622f9ebf-4e34-4693-8a51-a02f242e08ae.png)

-В скрипт-файл RollerAgent.cs добавила следующий код:
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
![image_6](https://user-images.githubusercontent.com/103308669/197758386-39d47b45-5266-46e6-b6f8-1c268d958757.png)
![image_7](https://user-images.githubusercontent.com/103308669/197758402-01d6c25d-46d8-4f19-8116-d133ac8ebf77.png)

-Объекту «сфера» добавила компоненты Rigidbody, Decision Requester, Behavior Parameters и настроила их.
![image_8](https://user-images.githubusercontent.com/103308669/197758523-b5051ca9-b8f4-4153-aa08-3bdd5ad86fcf.png)

-В корень проекта добавила файл конфигурации нейронной сети.
```python
behaviors:
  RollerBall:
    trainer_type: ppo
    hyperparameters:                 
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings: 
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000
```

-Запустила работу ml-агента.
![image_9](https://user-images.githubusercontent.com/103308669/197759364-b6a545d8-ecda-4534-9118-d0f6d6fdcdfd.png)

https://user-images.githubusercontent.com/103308669/197760211-32b1d03e-f28b-4c42-b69b-5a505bb6f732.mp4

-Далее я сделала 3, 9, 27 копий модели «Плоскость-Сфера-Куб», запустила симуляцию сцены и наблюдала за результатом обучения модели.
https://user-images.githubusercontent.com/103308669/197760419-e6ee9a9c-b734-46af-adb3-d753efc95db2.mp4

https://user-images.githubusercontent.com/103308669/197760466-57542e04-bbab-4cbb-ae43-e842a3413da4.mp4

https://user-images.githubusercontent.com/103308669/197760497-d8c7b408-2fe7-45dd-8c6b-fa8f4d303b2b.mp4

-После завершения обучения проверила работу модели. Вот, что получилось:
![image_10](https://user-images.githubusercontent.com/103308669/197765360-f05d10a3-30e1-4d75-b132-190ca2a8c81d.png)

https://user-images.githubusercontent.com/103308669/197760653-1353c7ed-0399-44e1-8146-a96919230a0a.mp4

-После обучения модели видно, что шарик стал двигаться напрямую к кубику, перестал постоянно падать за пределы платформы и стал быстрее находить кубик. 

## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.
```python
behaviors:
  RollerBall:                        #Имя агента
    trainer_type: ppo                #Устанавливаем режим обучения (Proximal Policy Optimization).
    hyperparameters:                 #Задаются гиперпараметры.
      batch_size: 10                 #Количество опытов на каждой итерации для обновления экстремумов функции.
      buffer_size: 100               #Количество опыта, которое нужно набрать перед обновлением модели.
      learning_rate: 3.0e-4          #Устанавливает шаг обучения (начальная скорость).
      beta: 5.0e-4                   #Отвечает за случайность действия, повышая разнообразие и иследованность пространства обучения.
      epsilon: 0.2                   #Порог расхождений между старой и новой политиками при обновлении.
      lambd: 0.99                    #Определяет авторитетность оценок значений во времени. Чем выше значение, тем более авторитетен набор предыдущих оценок.
      num_epoch: 3                   #Количество проходов через буфер опыта, при выполнении оптимизации.
      learning_rate_schedule: linear #Определяет, как скорость обучения изменяется с течением времени, линейно уменьшает скорость.
    network_settings:                #Определяет сетевые настройки.
      normalize: false               #Отключается нормализация входных данных.
      hidden_units: 128              #Количество нейронов в скрытых слоях сети.
      num_layers: 2                  #Количество скрытых слоев для размещения нейронов.
    reward_signals:                  #Задает сигналы о вознаграждении.
      extrinsic:
        gamma: 0.99                  #Коэффициент скидки для будущих вознаграждений.
        strength: 1.0                #Шаг для learning_rate.
    max_steps: 500000                #Общее количество шагов, которые должны быть выполнены в среде до завершения обучения.
    time_horizon: 64                 #Количество циклов ML агента, хранящихся в буфере до ввода в модель.
    summary_freq: 10000              #Количество опыта, который необходимо собрать перед созданием и отображением статистики.
```

-Decision Requester - запрашивает решение через регулярные промежутки времени и обрабатывает чередование между ними во время обучения.
-Behavior Parameters - определяет принятие объектом решений, в него указывается какой тип поведения будет использоваться: уже обученная модель или удалённый процесс обучения.

## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости.
-Сначала я создала еще один кубик на платформе.
![image_11](https://user-images.githubusercontent.com/103308669/197765396-0072dbe2-c3a3-49d6-9eb4-41f1c8bc7f6b.png)

-Далее я обновила код в скрипте, чтобы у шара появилась новая цель (новый кубик).
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public Transform Target2;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
        Target2.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);
        float distanceToTarget2 = Vector3.Distance(this.transform.localPosition, Target2.localPosition);

        if (distanceToTarget < 1.42f || distanceToTarget2 < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```

-Затем, как в Задании 1, я запустила обучение модели.
https://user-images.githubusercontent.com/103308669/197765912-f07525df-fa5d-4e29-8ca4-fd9568bf1e89.mp4

-Сделала 3, 9 и 27 копий модели и снова запустила обучение.
https://user-images.githubusercontent.com/103308669/197766080-8a99d2e2-ffd7-44db-b46d-53ed7b862f61.mp4

https://user-images.githubusercontent.com/103308669/197766102-ff3e3c5e-3677-44e9-8e3f-7361c885f5bf.mp4

https://user-images.githubusercontent.com/103308669/197766118-25ee32ef-f21b-4cea-bded-917355260e9f.mp4

-Вот, что получилось в итоге:
https://user-images.githubusercontent.com/103308669/197766217-144ea386-9f3e-4f3c-ae30-968fded3962f.mp4

##Выводы
Игровой баланс в играх - это субъективное «равновесие» между персонажами, командами, тактиками игры и другими игровыми объектами. Игровой баланс является одним из требований к «честности» правил.
Относительно простой нейронной сети достаточно, чтобы достичь высокой эффективности игры против игроков и традиционного игрового ИИ. Таких агентов можно использовать различными способами, например, для тренировки новых игроков или для выявления неожиданных стратегий. Так же системы машинного обучения могут использоваться для того, чтобы выявить дисбаланс в игре. 
