---
title: "Динозаврик google chrome | Создание самой простой игры на JavaScript"
date: 2023-10-01
image: image.png
description: 
categories:
    - web
tags:
    - js
---

![Alt text](1_PfFUiNbOCCgC6ZQ3EwiKUA.gif)

# Динозаврик google chrome на чистом JavaScript

## Создание файлов

Сделайте 3 файла:

- script.js
- index.html
- style.css

![Alt text](image-1.png)

## Скопируйте базовый шаблон HTML:

    <!DOCTYPE html>
    <html lang="ru">
        <head>
            <meta charset="UTF-8">
            <title>ДИНОЗАВРИК GOOGLE CHROME CHROME</title>
        </head>
    </html>

![Alt text](image-2.png)

## Добавьте стили и скрипты

Поместите в < head >

    <link rel="stylesheet" href="style.css">

Поместите в < html >

    <script src="script.js"></script>

![Alt text](image-3.png)

## Пришло время сделать настоящую игру!

Определим объекты игры:

    <body>
        <div id="game">
            <div id="dino"></div>
            <div id="cactus"></div>
        </div>
    </body>

Внутри game находятся два объекта - dino и cactus

game - фон игры

dino - динозаврик (главный управляемый персонаж)

cactus - кактусы (враги)


![Alt text](image-4.png)

## Оденем динозавра, покрасим кактусы и поклеим обои - style.css

### Определим окно игры (класс game):

    #game{
        width: 500px;
        height: 200px;
        border: 1px solid black;
    }

![Добавили стили для фона игры](image-5.png)

![Фон можно сделать больше](image-7.png)


### Создайте персонажа, добавив несколько стилей к символу div.

    #dino {
        width: 20px;
        height: 50px;
        background-color: red;
    }


![Стиль для динозаврика](image-15.png)

![Пока он низкополигональный](image-9.png)

Давайте поместим персонажа в нижнюю часть коробки.

    position: relative;
    top: 150px;


![Alt text](image-16.png)

![Alt text](image-11.png)

Блок игры имеет высоту 200 пикселей, а наш персонаж - 50 пикселей, поэтому нам нужно опустить его на 150 пикселей, чтобы поместить в самый низ.

### Кактусы...

    #cactus{
        width: 20px;
        height: 20px;
        background-color: blue;
        position: relative;
        top: 130px;
        left: 480px;
    }


![Alt text](image-17.png)

На этот раз мы сдвигаем его до упора вправо.

    ПОЛОЖЕНИЕ КАКТУСА = ШИРИНА ИГРЫ - ШИРИКА КАКТУСА
    480px = 500px - 20px


![Alt text](image-13.png)


## Добавляем анимацию кактусу

Чтобы наш кактус скользил по направлению к блоку, мы создаем анимацию и добавляем ее в блок.


    #cactus{
        width: 20px;
        height: 20px;
        background-color: blue;
        position: relative;
        top: 130px;
        left: 480px;

        
        animation: cactus 1s;
    }

    @keyframes cactus {
        0%{left:480px;}
        100%{left:-40px;}
    }


![Alt text](image-18.png)


### Бесконечная анимация


Чтобы анимация повторялась, мы добавляем бесконечность в анимацию

    animation: cactus 1s infinite;


![Alt text](1_sLC6uUoRTuMvBKmLw-UFHw.gif)

### Линейная анимация

    animation: cactus 1s infinite linear;


## Добавляем анимацию динозавру

Теперь для прыжков, мы создадим вторую анимацию, в которой мы заставляем динозавра прыгать наверх.

Ставим 30 и 70%. Это заставляет его подпрыгивать вверх, немного зависать, а затем снова опускаться.


    @keyframes jump{
        0%{top: 150px;}
        30%{top: 100px;}
        70%{top: 100px;}
        100%{top: 150px;}
    }


![Alt text](image-19.png)


Так же, как и в случае с кактусом, мы добавляем анимацию к нашему динозавру.

    animation: jump 500ms infinite;


![Alt text](image-20.png)


![Alt text](1_uJz92hZHGQJO4e-4rWEvGA.gif)


## Управляем динозавром

Мы хотим, чтобы он прыгал, когда мы вызываем функцию JavaScript, которую мы собираемся создать.

### Удаление анимации

Для этого надо удалить анимацию прыжка у динозавра, чтобы он не прыгал сам по себе:

![Alt text](image-21.png)
![Alt text](image-22.png)

### Создание класса анимации

    .animate{
        animation: jump 500ms;
    }

![Alt text](image-23.png)

### Пишем JavaScript код

Мы начинаем с создания двух переменных для доступа к нашему динозавру и кактусу.

В файле script.js:

    let character = document.getElementById("dino")
    let block = document.getElementById("cactus")

![Alt text](image-24.png)

Создадим функцию прыжка:

    function jump() {
        character.classList.add("animate")
    }

![Alt text](image-25.png)

Чтобы запустить нашу функцию, нам нужно добавить onClick в наш index.html (HTML-файл).

    <html lang="en" onclick = "jump()">

![Alt text](image-26.png)

### Почему он прыгает всего один раз?

Добавим удаление класса animate через 500 миллисекунд:

    setTimeout(function(){
        character.classList.remove("animate")
    }, 500)

![Alt text](image-27.png)

