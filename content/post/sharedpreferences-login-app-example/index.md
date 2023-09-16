---
title: Приложение для авторизации на Android с использованием SharedPreferences
description: Приложение для авторизации на Android с использованием SharedPreferences 
slug: sharedpreferences-login-app-example
date: 2023-06-23 00:00:00+0000
image: cover.jpg
tags: 
    - java
    - android
categories:
    - android
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

> Репозиторий с исходным кодом [https://github.com/LencoDigitexer/Sharedpreferences_Login_App_Example](https://github.com/LencoDigitexer/Sharedpreferences_Login_App_Example)

## Описание

Приложение состоит из двух экранов:

1. Экран авторизации (MainActivity): Здесь пользователь вводит свое имя и электронную почту. После успешной авторизации данные сохраняются с помощью SharedPreferences.
2. Экран профиля (HomeActivity): На этом экране отображаются сохраненные имя и электронная почта. Пользователь может также выйти из аккаунта, и данные будут удалены из SharedPreferences.

## Технологии и функциональность

- SharedPreferences: Для хранения и получения данных пользователя (имя и email).
- EditText и TextView: Для ввода данных пользователем и отображения сохраненных данных.
- Button: Для сохранения данных и выхода из аккаунта.
- Toast: Для отображения уведомлений об успешной авторизации и выходе из аккаунта.

## Демо
<https://github.com/LencoDigitexer/Sharedpreferences_Login_App_Example/assets/44779327/02ac560c-b33e-4b64-8e7d-41891a819d97>

## Пример кода

### MainActivity.java

```java
// Вставьте код класса MainActivity сюда
package com.lencodigitexer.sharedpreferences_login_app_example;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    // Объявление переменных для работы с элементами интерфейса
    EditText editText_name, editText_email;
    Button button_save;

    // Объект SharedPreferences для сохранения данных
    SharedPreferences sharedPreferences;

    // Ключи для сохранения данных в SharedPreferences
    private static final String SHARED_PREF_NAME = "mypref";
    private static final String KEY_NAME = "name";
    private static final String KEY_EMAIL = "email";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Получение объекта SharedPreferences по имени "mypref"
        sharedPreferences = getSharedPreferences(SHARED_PREF_NAME, MODE_PRIVATE);

        // Проверяем, сохранено ли имя в SharedPreferences
        String name = sharedPreferences.getString(KEY_NAME, null);

        // Если имя уже сохранено, переходим на экран HomeActivity
        if (name != null){
            Intent intent = new Intent(MainActivity.this, HomeActivity.class);
            startActivity(intent);
        } else {
            // Если имя не сохранено, отображаем макет activity_main
            setContentView(R.layout.activity_main);

            // Привязываем переменные к элементам интерфейса
            editText_name = findViewById(R.id.edittext_name);
            editText_email = findViewById(R.id.edittext_email);
            button_save = findViewById(R.id.button_save);
        }

        // Добавляем обработчик клика на кнопку "Сохранить"
        button_save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {

                // Получаем редактор SharedPreferences для внесения изменений
                SharedPreferences.Editor editor = sharedPreferences.edit();

                // Сохраняем введенные имя и email в SharedPreferences
                editor.putString(KEY_NAME, editText_name.getText().toString());
                editor.putString(KEY_EMAIL, editText_email.getText().toString());

                // Применяем изменения
                editor.apply();

                // Переходим на экран HomeActivity
                Intent intent = new Intent(MainActivity.this, HomeActivity.class);
                startActivity(intent);

                // Выводим всплывающее уведомление об успешной авторизации
                Toast.makeText(MainActivity.this, "Успешная авторизация", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

### HomeActivity.java

```java

package com.lencodigitexer.sharedpreferences_login_app_example;

import androidx.appcompat.app.AppCompatActivity;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

public class HomeActivity extends AppCompatActivity {

    // Объявление переменных для работы с элементами интерфейса
    TextView textView_name, textView_email;
    Button button_logout;

    // Объект SharedPreferences для сохранения данных
    SharedPreferences sharedPreferences;

    // Ключи для доступа к сохраненным данным в SharedPreferences
    private static final String SHARED_PREF_NAME = "mypref";
    private static final String KEY_NAME = "name";
    private static final String KEY_EMAIL = "email";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_home);

        // Привязываем переменные к элементам интерфейса
        textView_name = findViewById(R.id.text_fullname);
        textView_email = findViewById(R.id.text_email);
        button_logout = findViewById(R.id.button_logout);

        // Получаем объект SharedPreferences по имени "mypref"
        sharedPreferences = getSharedPreferences(SHARED_PREF_NAME, MODE_PRIVATE);

        // Получаем сохраненные имя и email из SharedPreferences
        String name = sharedPreferences.getString(KEY_NAME, null);
        String email = sharedPreferences.getString(KEY_EMAIL, null);

        // Если имя или email не равны null, выводим их на экран
        if (name != null || email != null){
            textView_name.setText("Имя - " + name.toString());
            textView_email.setText("Почта - " + email.toString());
        }

        // Добавляем обработчик клика на кнопку "Выход"
        button_logout.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {

                // Получаем редактор SharedPreferences для внесения изменений
                SharedPreferences.Editor editor = sharedPreferences.edit();

                // Очищаем все сохраненные данные в SharedPreferences
                editor.clear();

                // Применяем изменения
                editor.commit();

                // Выводим всплывающее уведомление о успешном выходе из аккаунта
                Toast.makeText(HomeActivity.this, "Выход успешно завершён", Toast.LENGTH_SHORT).show();

                // Завершаем текущую активити (экран) и возвращаемся назад
                finish();
            }
        });
    }
}

```