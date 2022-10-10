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
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.

- В облачном сервисе google console подключить API для работы с google sheets и google drive.

![image_1](https://user-images.githubusercontent.com/103308669/194856904-77b6aee9-cae4-4c05-b894-1113a5274d3b.png)
![image_2](https://user-images.githubusercontent.com/103308669/194862279-b7ba7b84-269f-4655-857d-89c55391f05b.png)
![image_3](https://user-images.githubusercontent.com/103308669/194862301-d2041a26-5ad4-445d-9e0f-7c09a36cc1b7.png)
![image_13](https://user-images.githubusercontent.com/103308669/194862316-c6d155de-4dc4-4de8-84cb-c304ad1a2358.png)

- Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

```py
import gspread
import numpy as np
gs = gspread.service_account(filename='celestial-feat-364617-38d786a51576.json')
sh = gs.open("UnitySheets")
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i-1]-price[i-2])/price[i-2])*100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)
```

![image_4](https://user-images.githubusercontent.com/103308669/194857117-cc2e4814-f1aa-4b4b-bac8-39326e57e6c3.png)
![image_5](https://user-images.githubusercontent.com/103308669/194857152-86e4c688-8c92-4d99-bb07-8a168212b503.png)
![image_6](https://user-images.githubusercontent.com/103308669/194857242-7e734f4b-f1c0-4122-ab56-4d645ba4ebe3.png)

- Создать новый проект на Unity, который будет получать данные из google-таблицы.

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1qh1aFQZOrx29qXiso0dvL-unAp7F_1ggMiu3A-Uuyes/values/Лист1?key=AIzaSyDJNeqtiLyZai_ueRr9nKzoiZGw6vpe3pQ");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }
```

![image_7](https://user-images.githubusercontent.com/103308669/194857345-0a383a7a-6fa7-4ac7-8d61-ca58befefd4f.png)
![image_8](https://user-images.githubusercontent.com/103308669/194857382-f6238e3e-fc22-4c0e-afcd-928d5dd6b871.png)

- Написать функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы.

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1qh1aFQZOrx29qXiso0dvL-unAp7F_1ggMiu3A-Uuyes/values/Лист1?key=AIzaSyDJNeqtiLyZai_ueRr9nKzoiZGw6vpe3pQ");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

![image_9](https://user-images.githubusercontent.com/103308669/194857460-331b49fb-0659-4521-af53-b177cb61d3c5.png)

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1.

```py
import gspread
import numpy as np

def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()

def model(a, b, x):
    return a * x + b

def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db
    return a, b

def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b

gc = gspread.service_account(filename='celestial-feat-364617-38d786a51576.json')
sh = gc.open("UnitySheets")

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)
a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.000001
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0

while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        a, b = iterate(a, b, x, y, 100)
        prediction = model(a, b, x)
        loss = loss_function(a, b, x, y)
        tempInf = loss
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(tempInf))
        print(tempInf)
```

![image_10](https://user-images.githubusercontent.com/103308669/194857685-01d7af5b-3e72-4a18-be5b-6d6e9050622b.png)
![image_11](https://user-images.githubusercontent.com/103308669/194857703-b46d6f91-f2c7-42cf-a161-649d639a8cf6.png)

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.
Изменение диапазонов и количества колонок:

```c#
void Update()
{
    if (dataSet["Mon_" + i.ToString()] <= 195 & statusStart == false & i != dataSet.Count)
    {
        StartCoroutine(PlaySelectAudioGood());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }

    if (dataSet["Mon_" + i.ToString()] > 195 & dataSet["Mon_" + i.ToString()] < 500 & statusStart == false & i != dataSet.Count)
    {
        StartCoroutine(PlaySelectAudioNormal());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }

    if (dataSet["Mon_" + i.ToString()] >= 500 & statusStart == false & i != dataSet.Count)
    {
        StartCoroutine(PlaySelectAudioBad());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }
}

IEnumerator GoogleSheets()
{
    UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1qh1aFQZOrx29qXiso0dvL-unAp7F_1ggMiu3A-Uuyes/values/Лист1?key=AIzaSyDJNeqtiLyZai_ueRr9nKzoiZGw6vpe3pQ");
    yield return curentResp.SendWebRequest();
    string rawResp = curentResp.downloadHandler.text;
    var rawJson = JSON.Parse(rawResp);
    foreach (var itemRawJson in rawJson["values"])
    {
        var parseJson = JSON.Parse(itemRawJson.ToString());
        var selectRow = parseJson[0].AsStringList;
        dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]));
    }
}
```

![image_12](https://user-images.githubusercontent.com/103308669/194857787-5000b455-6d20-44c8-807d-f40aa319f074.png)

## Выводы

В ходе выполнения данной лабораторной работы, я реализовала запись данных из скрипта на Python в google-таблицу, при этом записывая в нее свои значения. Также у меня получилось создать проект на Unity, который будет получать данные из google-таблицы и выводить их в консоль. Я написала функционал на Unity, который воспроизводит аудио-файл в зависимости от значения данных из таблицы.
