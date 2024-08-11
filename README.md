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

![рис 2](https://github.com/ysatii/network_protection/blob/main/img/image0_2.jpg)

![рис 3](https://github.com/ysatii/network_protection/blob/main/img/image0_3.jpg)

![рис 4](https://github.com/ysatii/network_protection/blob/main/img/image0_4.jpg)

## Задание 1
Проведите разведку системы и определите, какие сетевые службы запущены на защищаемой системе:

**sudo nmap -sA < ip-адрес >**  

**sudo nmap -sT < ip-адрес >**  

**sudo nmap -sS < ip-адрес >**  

**sudo nmap -sV < ip-адрес >**  

По желанию можете поэкспериментировать с опциями: https://nmap.org/man/ru/man-briefoptions.html.

### В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.

## Решение 1 
 
для выполнения задачи подготовим две машины  
1. машина для произведения атак 192.168.4.26   
2. атакуемая машина 192.168.4.70   
на машине 192.168.4.70 отключаем меж сетевойэкран командой  
```
sudo ufw disable
```
на атакуемой машине 

Откроем окно и выполним команду, для запуска системы детектирования атак
```
sudo suricata -c /etc/suricata/suricata.yaml -i enp0s3 
```

Откроем еще окно для просмотра логов suricata и выполним команду  
```
sudo tail f /var/log/suricata/fast.log
```
в этом оке будем сотреть логи поле выполнения команд на операционной системе kali-linux

1. **sudo nmap -sA 192.168.4.70  **  
![рис 1](https://github.com/ysatii/network_protection/blob/main/img/image1_1.jpg)  
нет записей в логе

2. **sudo nmap -sT 192.168.4.70  **  

![рис 2](https://github.com/ysatii/network_protection/blob/main/img/image1_2.jpg)  
![рис 3](https://github.com/ysatii/network_protection/blob/main/img/image1_3.jpg)  
Опция сканурует открытые порт TCP  
22/tcp   open  ssh  
7070/tcp open  realserver  

открыт порт SSH !!  

3. **sudo nmap -sS 192.168.4.70  **   
![рис 4](https://github.com/ysatii/network_protection/blob/main/img/image1_4.jpg)  
![рис 5](https://github.com/ysatii/network_protection/blob/main/img/image1_5.jpg)   
(TCP SYN сканирование) .
SYN это используемый по умолчанию и наиболее популярный тип сканирования. На то есть
несколько причин. Он может быть быстро запущен, он способен сканировать тысячи портов
в секунду при быстром соединении, его работе не препятствуют ограничивающие
бранмауэры. Этот тип сканирования относительно ненавящив и незаметен, т.к. при таком
сканировании TCP соединение никогда не устанавливается до конца  


4. **sudo nmap -sV 192.168.4.70  **    
Исследуем службу версии   
![рис 6](https://github.com/ysatii/network_protection/blob/main/img/image1_6.jpg)  
![рис 7](https://github.com/ysatii/network_protection/blob/main/img/image1_7.jpg)  

## Установим Fail2Ban
```
sudo apt install fail2ban
```

![рис 8](https://github.com/ysatii/network_protection/blob/main/img/image1_8.jpg)  

проверим статус Fail2Ban  
```
sudo systemctl status fail2ban  
```
![рис 9](https://github.com/ysatii/network_protection/blob/main/img/image1_9.jpg)  

## Выполним теже команды с включенным  Fail2Ban  

1. **sudo nmap -sA 192.168.4.70**
нет записей в логе  

2. **sudo nmap -sT 192.168.4.70**
![рис 10](https://github.com/ysatii/network_protection/blob/main/img/image1_10.jpg)  


3. **sudo nmap -sS 192.168.4.70**
![рис 10](https://github.com/ysatii/network_protection/blob/main/img/image1_11.jpg)  


4. **sudo nmap -sV 192.168.4.70**
![рис 10](https://github.com/ysatii/network_protection/blob/main/img/image1_12.jpg)  

лог сервиса Fail2Ban
![рис 10](https://github.com/ysatii/network_protection/blob/main/img/image1_13.jpg) 


сервис Fail2Ban во время атаки был в рабочем состоянии
изменений в логе нет, сам Fail2Ban работает на снове статистики, считаю что данных мало для срабатывания! Либо необходимо провести настройку для защиты портов!

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

принудительно выключем Fail2Ban, така как он прекрасно засекает аткау на порт SSH с настройками по умолчанию
```
systemctl stop fail2ban
```
наблюдаем сброс соеденения
![рис 1](https://github.com/ysatii/network_protection/blob/main/img/image2_1.jpg) 

принудительно выключим fail2ban для чистоты эскперемента  
![рис 2](https://github.com/ysatii/network_protection/blob/main/img/image2_2.jpg)  

удалось подобрать пароль!  
![рис 3](https://github.com/ysatii/network_protection/blob/main/img/image2_3.jpg)  

лог Suricata  
![рис 4](https://github.com/ysatii/network_protection/blob/main/img/image2_4.jpg)  

лог Fail2Ban
удалось подобрать пароль!  
![рис 5](https://github.com/ysatii/network_protection/blob/main/img/image2_5.jpg)  

лог Suricata  
![рис 6](https://github.com/ysatii/network_protection/blob/main/img/image2_6.jpg)