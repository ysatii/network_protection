# Домашнее задание к занятию «Защита сети» - Мельник Юрий Александрович

## Подготовка к выполнению заданий

   1. Подготовка защищаемой системы:

   - установите Suricata,
   - установите Fail2Ban.

   2. Подготовка системы злоумышленника: установите nmap и thc-hydra либо скачайте и установите Kali linux.

Обе системы должны находится в одной подсети.

## Произведем подготовку ситемы!

Установим необходимые пакеты   
```
sudo apt -y install libpcre3 libpcre3-dev build-essential autoconf automake libtool libpcap-dev libnet1-dev libyaml-0-2 libyaml-dev zlib1g zlib1g-dev libmagic-dev libcap-ng-dev libjansson-dev pkg-config libnetfilter-queue-dev geoip-bin geoip-database geoipupdate apt-transport-https
```

Подключаем внешний репозиторий:   
```
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt-get update
```


Устанавливаем последнюю стабильную версию Suricata:  
```
sudo apt-get install suricata
```

Настройки по умолчанию хранятся в файле /etc/default/suricata, 
```
sudo nano /etc/default/suricata
```

а пользовательские — в /etc/suricata/suricata.yaml. 
```
sudo nano /etc/suricata/suricata.yaml
```

Чтобы завершить настройку потребуется установить suricata-update для обновления и загрузки правил.
```
sudo apt install python-pip
sudo pip install pyyaml
sudo pip install <a href="https://github.com/OISF/suricata-update/archive/master.zip">https://github.com/OISF/suricata-update/archive/master.zip</a>
sudo pip install --pre --upgrade suricata-update
```
Дальше нам нужно запустить команду suricata-update для установки набора правил Emerging Threats Open:
```
sudo suricata-update 
```

![рис 1](https://github.com/ysatii/network_protection/blob/main/img/image0_1.jpg)

[рис 2](https://github.com/ysatii/network_protection/blob/main/img/image0_2.jpg)

[рис 3](https://github.com/ysatii/network_protection/blob/main/img/image0_3.jpg)

[рис 4](https://github.com/ysatii/network_protection/blob/main/img/image0_4.jpg)

## Задание 1
Проведите разведку системы и определите, какие сетевые службы запущены на защищаемой системе:

**sudo nmap -sA < ip-адрес >**  

**sudo nmap -sT < ip-адрес >**  

**sudo nmap -sS < ip-адрес >**  

**sudo nmap -sV < ip-адрес >**  

По желанию можете поэкспериментировать с опциями: https://nmap.org/man/ru/man-briefoptions.html.

### В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.

## Решение 1 
 
 





## Задание 2

Проведите атаку на подбор пароля для службы SSH:  
**Проведите атаку на подбор пароля для службы SSH:**  

1. Настройка hydra: 
- создайте два файла: users.txt и pass.txt;
- в каждой строчке первого файла должны быть имена пользователей, второго — пароли. В нашем случае это могут быть случайные строки, но ради эксперимента можете добавить имя и пароль существующего пользователя.

Дополнительная информация по hydra: https://kali.tools/?p=1847.  


2. Включение защиты SSH для Fail2Ban:

- открыть файл /etc/fail2ban/jail.conf,
- найти секцию ssh,
- установить enabled в true.

Дополнительная информация по Fail2Ban:https://putty.org.ru/articles/fail2ban-ssh.html.  

### В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.
## Решение 2

