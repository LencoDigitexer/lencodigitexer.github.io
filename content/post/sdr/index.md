---
title: Как я запустил SDR-радио в интернет
description: Как я запустил SDR-радио в интернет
slug: sdr
date: 2021-07-07 00:00:00+0000
image: cover.jpg
categories:
    - sdr
tags:
    - sdr
    - vpn
---

GA(good afternoon), дорогие читатели уютного блога, websdr.org-подобной радиостанции. Вчера я запустил сервис в бета-режиме, создал полигон для развития этого сервера. С вами я, любознательный анонимус, и как я докатился до жизни такой? - Сейчас расскажу

![img](https://telegra.ph/file/a33c8293c9ff8801fa9c5.png)

Это я, позер

![img](https://telegra.ph/file/bd2abbf3a8bfc4319302c.png)

Вот как это выглядело 07.07.2021 числа

## Оборудование

Купил синий свисток на [TRL2832 + 820T2](https://www.aliexpress.com/wholesale?catId=0&SearchText=sdr+rtl2832u+r820t2) обошелся в 600 рублей.

![img](https://telegra.ph/file/abad600ef83ef7f67863e.jpg)

покоритель женских сердец

**Сервером** выступает нетбук 1232432 года до нашей эры, великий hp mini 110 410er на intel atom. Был всегда, возможно старше меня. 
**Антенна** - диполь от телевизора, длинной 50 см плечо или метр в общую длину, что позволяет качественно ловить диапазон в районе 143 Mhz, т.е. телевидение. Нашел на чердаке.

![img](https://telegra.ph/file/de1545da12c0a21f84dde.jpg)

Диполь от телевизора, подключенная клеммами к "свистку"

## Установка локального сервера

1) Скачиваю образ Ubuntu 16.04, ставлю на нетбук

2) Устанавливаю rtl-sdr драйвер

```bash
sudo apt-get install libusb-1.0-0-dev git cmake
git clone git://git.osmocom.org/rtl-sdr.git cd rtl-sdr/
mkdir build
cd build 
cmake ../ -DINSTALL_UDEV_RULES=ON 
make
sudo make install 
sudo cp ../rtl-sdr.rules /etc/udev/rules.d/ 
sudo ldconfig
```

3) Блокирую ненужные функции драйвера

```bash
cat <no-rtl.conf 
blacklist dvb_usb_rtl28xxu
blacklist rtl2832 
blacklist rtl2830 
EOF
sudo mv no-rtl.conf /etc/modprobe.d/
```

4) Ставлю библиотеки для сервера

```bash
sudo apt-get update && sudo apt-get install libx11- xcb1 libxss1 libasound2 libxkbfile1 sudo apt-get install libfftw3-dev libfftw3-3 libpng-dev 
sudo apt-get install libasound2 libasound2-dev
sudo apt-get install -f libpng-dev
```

<br>

5) Беру дистрибутив websdr и правлю конфиги, а конкретно настраиваю диапазон.


> *# Начальная точка, откуда будет проигрываться звук*

```bash
*initial 100000 fm*

*allowwide*

*#BAND 2m*

*band 150MHz*

*device !rtlsdr 192.168.1.100:1234*

*# Samplerate in Hz:*

*samplerate 2048000*

*# Center frequency in kHz:*

*centerfreq 100000*

*# A short description of the antenna, for listing on websdr.org:*

*antenna GP*

*gain -30*

*#Added extra zoom function*

*extrazoom 1*

*#Added noise blankerfunction*

*#noiseblanker 100*
```




6) Запускаю rtl_tcp командой

```bash
rtl_tcp -a 192.168.1.100 -p1234
```

Можно открыть sdrSharp и выбрав RTL(TCP) вбить этот диапазон, выбрать модуляцию 2.048 mbps, и наслаждаться любым видом контента, не ограничиваясь длинной пропускной способностью приемника в 30khz, в то время, когда обычный FM-поток имеет 190khz. Т.е. послушать нормальный FM не получится.

7) Запускаю websdr из папки дистрибутива

```bash
sudo ./websdr
```

8) ??? Profit. Через локалку заходим к серверу в гости и видим, что в локалке работает. Теперь надо перекинуть мостик через VDS сервер.

<br>

## Установка VPN

1)  Купил сервер на хостинге за 189 рублей

![img](https://telegra.ph/file/fe09f3624415ed54cfc05.png)

Догадались, что за хостинг?

2) Две команды

```bash
get https://git.io/vpn -O openvpn-install.sh
bash openvpn-install.sh
```

Настройка сервер VPN легкая, делал по этому гайду:

{{< youtube "VBWydSqksFY" >}}

3) Так как Ubuntu 16.04 доисторической хлам, то OpenVPN клиента, поддерживающего TLS шифрования в официальных репозиториях нет, иду в гугл и натыкаюсь на [решение](https://gist.github.com/tienthanh2509/50094f55439e19af3a09911aa5c48852):

```bash
curl -s https://swupdate.openvpn.net/repos/repo-public.gpg | apt-key add -
echo "deb http://build.openvpn.net/debian/openvpn/stable xenial main" > /etc/apt/sources.list.d/openvpn-aptrepo.list
apt update
apt install -y openvpn
```

Благо, i386 поддерживается и легаси ПО будет теперь преследовать меня всегда, пока товарищ PA3FWM не откроет код ядра websdr И не даст людям продвигать ПО своими силами, пока тру-радиолюбитель медленно закрывает проект, забрасывая его.

4) Скачиваю получившийся конфиг ovpn, подключаюсь на ноутбуке командой

```bash
sudo openvpn test.ovpn
```

5) Проверяю смену ip:

```bash
curl ifconfig.me
```

Готово, ВПН настроен и ip сменился, пора двигаться дальше.

## Белый IP для всех

Я хотел, чтобы имея VPN - соединение, сервер за 189 рублей предоставлял моему ноутбуку белый ip, чтобы из любой точки интернета можно подключиться к нему.

1) Проверяю на ноуте локальный ip внутри VPN сети

```bash
ifconfig
```

<br>

```bash
inet addr:10.8.0.2 P-t-P:10.8.0.2 Mask:255.255.255.0
UP POINTOPOINT RUNNING NOARP MULTICAST MTU:1500 Metric:1
RX packets:791532 errors:0 dropped:0 overruns:0 frame:0
TX packets:428050 errors:0 dropped:58778 overruns:0 carrier:0
collisions:0 txqueuelen:500 
RX bytes:328839957 (328.8 MB) TX bytes:258639628 (258.6 MB)
```

2) На сервере правлю iptables

```bash
iptables -t nat -A PREROUTING -p tcp -d 92.53.120.26 —dport 8080 -j DNAT —to-destination 10.8.0.2:80
```

Теперь можно зайти на [http://92.53.120.26:8080/](http://92.53.120.26:8080/) и увидеть локальный сервер.

![img](https://telegra.ph/file/0da7ac9b36effeeba9cc2.jpg)

Готово.

## Монитор ресурсов для локального сервера

Это нужно для того, чтобы смотреть нагрузку на сервер, решать проблемы доступа, анализировать трафик... да вы чего, просто я люблю графики, это же прикольно наблюдать, как какая-то железяка трудится и показывает свои характеристики.

![img](https://telegra.ph/file/80ba58989a639bbf8fa42.png)

Вот как это выглядит

1) Выбор пал на linux-dash, устанавливаю его


```bash
git clone --depth 1 https://github.com/afaqurk/linux-dash.git
cd linux-dash/app/server
```

2) Компилирую сервер на go

```bash
go run index.go
go build && ./server -h
```

3) Запускаю скомпилированный бинарник(каламбур)

```bash
./server -listen="0.0.0.0:5555"
```

4) Снова на сервере правлю iptables

```bash
iptables -t nat -A PREROUTING -p tcp -d 92.53.120.26 —dport 8081 -j DNAT —to-destination 10.8.0.2:5555
```

Но меняю порты. Можно зайти на [http://92.53.120.26:8081/](http://92.53.120.26:8080/) и увидеть анализ ресурсов.

## Вывод

Готов полигон для развития, настроены VPN, iptables. Теперь развитие будет вестись в уклон апгрейда железа, настройки доступа. И скорее всего будет использован [OpenWebRX](https://github.com/jketterl/openwebrx)  как здоровая опенсорсная альтернатива закрытому ядру websdr.

Спасибо, что дочитали до конца. Буду писать еще про радио и продвижения в этом деле. Буду писать про новые открытия и совершения.