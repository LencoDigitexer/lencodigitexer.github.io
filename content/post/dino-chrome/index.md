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

    #character {
        width: 20px;
        height: 50px;
        background-color: red;
    }

![Стиль для динозаврика](image-8.png)

![Пока он низкополигональный](image-9.png)

Давайте поместим персонажа в нижнюю часть коробки.

    position: relative;
    top: 150px;

![Alt text](image-10.png)

![Alt text](image-11.png)