# D02_Linux Network
## Part 1. Инструмент ipcalc
__ipcalc__ - это утилита, с помощью которой можно выполнять простые операции с адресами IPv4. Если набрать ipcalc без каких-либо входных параметров, то на экране появится красивая «справка» с примерами, которые очень полезны для начала работы.
* ___1.1. Сети и маски___
    1) Адрес сети 192.167.38.54/13 <br>
    ![](../misc/images_for_report/ipcalc_address_1.png)
    2) Перевод маски 255.255.255.0 в префиксную и двоичную запись, /15 в обычную и двоичную, 11111111.11111111.11111111.11110000 в обычную и префиксную <br>
        * Перевод в двоичную запись или в префиксную можно посмотреть по специальным таблицам или с помощью `ipcalc`:
        ![alt text](../misc/images_for_report/ipcalc_table_2.png)
        ![](../misc/images_for_report/ipcalc_netmask_2.png)
        * Аналогично можно смотреть `/15`: <br>
        ![alt text](../misc/images_for_report/ipcalc_from_normal_2.png)
        * И чтобы определить 11111111.11111111.11111111.11110000, нужно перевести из двоичной системы в десятичную, и далее просмотреть: <br>
        ![alt text](../misc/images_for_report/ipcalc_23.png)
    3)  Минимальный и максимальный хост в сети 12.167.38.4 при масках: /8, 11111111.11111111.00000000.00000000, 255.255.254.0 и /4 <br>
    В данном задании используется команда `ipcalc [address]`, в случае двоичной записи требуется перевод в десятичную запись<br>
        * 12.167.38.4/8:
            * HostMin:   12.0.0.1 
            * HostMax:   12.255.255.254 
        * 12.167.38.4/11111111.11111111.00000000.00000000 (= /16):
            * HostMin:   12.167.0.1
            * HostMax:   12.167.255.254 
        * 12.167.38.4/255.255.254.0:
            * HostMin:   12.167.38.1 
            * HostMax:   12.167.39.254 
        * 12.167.38.4/4:
            * HostMin:   0.0.0.1 
            * HostMax:   15.255.255.254 
* ___1.2. localhost___
    * Определи и запиши в отчёт, можно ли обратиться к приложению, работающему на localhost, со следующими IP: 194.34.23.100, 127.0.0.2, 127.1.0.1, 128.0.0.1 <br>
    Обратиться можно: 127.0.0.2 и 127.1.0.1, пример: <br>
    ![](../misc/images_for_report/ipcalc_loopback.png)
* ___1.3. Диапазоны и сегменты сетей___
    * Определи и запиши в отчёт:
    1) Какие из перечисленных IP можно использовать в качестве публичного, а какие только в качестве частных: 10.0.0.45, 134.43.0.2, 192.168.4.2, 172.20.250.4, 172.0.2.1, 192.172.0.1, 172.68.0.2, 172.16.255.255, 10.10.10.10, 192.169.168.1 <br>
    Для проверки используется тот же ipcalc, в строке Hosts/Net будет отображаться, открыта сеть, или она частная, пример: <br>
    ![alt text](../misc/images_for_report/ipcalc3.png) <br>
    Итого:
        * Публичные: 134.43.0.2, 172.0.2.1, 192.172.0.1, 172.68.0.2, 192.169.168.1<br>
        * Частные: 10.0.0.45, 192.168.4.2, 172.20.250.4, 172.16.255.255, 10.10.10.10<br>
    2) Какие из перечисленных IP адресов шлюза возможны у сети 10.10.0.0/18: 10.0.0.1, 10.10.0.2, 10.10.10.10, 10.10.100.1, 10.10.1.255 <br>
    ![alt text](../misc/images_for_report/ipcalc_last.png)<br>
    Здесь следует рассматривать диапазон [HostMin, HostMax]. <br>   * Не подходят: 10.10.100.1, 10.0.0.1, остальные подходят.

## Part 2. Статическая маршрутизация между двумя машинами
* Две вирутальные машины: <br>
![alt text](../misc/images_for_report/two_running_ws.png)
* С помощью команды ip a посмотри существующие сетевые интерфейсы. <br>
    * ws1: <br>
    ![alt text](../misc/images_for_report/ws1_ipa.png)
    * ws2: <br>
    ![alt text](../misc/images_for_report/image.png)
* Опиши сетевой интерфейс, соответствующий внутренней сети, на обеих машинах и задать следующие адреса и маски: ws1 - 192.168.100.10, маска /16, ws2 - 172.24.116.8, маска /12. <br>
    * ws1: lo - 127.0.0.1/8 и enp0s3 - 10.0.2.15/24 <br>
    * ws2: lo - 127.0.0.1/8 и enp0s3 - 10.0.2.15/24 <br>
    * Содержание `etc/netplan/00-installer-config.yaml` после изменения адресов и масок: <br>
    ![](../misc/images_for_report/image-2.png) <br>
    ![alt text](../misc/images_for_report/image-1.png) <br>
* Выполни команду netplan apply для перезапуска сервиса сети. <br>
    * ws1: <br>
    ![alt text](../misc/images_for_report/image-3.png)
    * ws2 <br>
    ![alt text](../misc/images_for_report/image-4.png)
* ___2.1. Добавление статического маршрута вручную___ <br>
Используются команды:
    * ws1: `sudo ip r add 172.24.116.8 dev enp0s3` и `ping 172.24.116.8 -c 10`
    * ws2: `sudo ip r add 192.168.100.10 dev enp0s3` и `ping 192.168.100.10 -c 10`
    * c [num] - число пакетов
    * ws1: <br>
    ![alt text](../misc/images_for_report/image-5.png)
    * ws2: <br>
    ![alt text](../misc/images_for_report/image-6.png)
* ___2.2. Добавление статического маршрута с сохранением___ <br>
    * Перезапуск машин: `reboot`
    * После перезапуска видим, что данные не сохранились: <br>
    ![alt text](../misc/images_for_report/notsavingiproute.png)
    * Добавь статический маршрут от одной машины до другой с помощью файла /etc/netplan/00-installer-config.yaml. <br>
        * ws1: <br>
        ![alt text](../misc/images_for_report/changestaticiproutews1.png)
        * ws2: <br>
        ![alt text](../misc/images_for_report/changestaticroutews2.png)
    * Пропингуй соединение между машинами. <br>
        * ws1: <br>
        ![alt text](../misc/images_for_report/pingstaticipws1.png)
        * ws2: <br>
        ![alt text](../misc/images_for_report/pingstaticipws2.png)

## Part 3. Утилита iperf3
* ___3.1. Скорость соединения___ <br>
    * Переведи и запиши в отчёт: 8 Mbps в MB/s, 100 MB/s в Kbps, 1 Gbps в Mbps. <br>
    * 8 Mbps = 1 MB/s, 100 MB/s = 800000 Kbps, 1Gbps = 1000 Mbps
*  ___3.2. Утилита iperf3___
    * Измерь скорость соединения между ws1 и ws2.
    1. В качестве сервера будет ws2: `iperf3 -s -f K` <br>
    ![alt text](../misc/images_for_report/iperf3server.png)
    2. В качестве клиента будет ws1: <br>
    ![alt text](../misc/images_for_report/operf3client.png)

## Part 4. Сетевой экран
* ___4.1. Утилита iptables___
    1. Создал файл /etc/firewall.sh, имитирующий фаерволл, на ws1 и ws2.
        * ws1: <br>
        ![alt text](../misc/images_for_report/firewallws1.png)
        * ws2: <br>
        ![alt text](../misc/images_for_report/firewallws2.png)
    2. Запустил файлы на обеих машинах командами `chmod +x /etc/firewall.sh` и `/etc/firewall.sh`. <br>
        * ws1: <br>
        ![alt text](../misc/images_for_report/firewall1.ws1.png)
        * ws2: <br>
        ![alt text](../misc/images_for_report/firewall2.ws2.png)
    3. Так как команды выполняются по порядку сврерху вниз, то в случае для первой машины ws1 пинг будет запрещен(потому что accept после drop), а для ws2 пинги разрешены. Применные праивла не могут быть переписаны.<br>
    ![alt text](../misc/images_for_report/pingafterfirewallws1.png) <br>
    ![](../misc/images_for_report/pingafterfirewallws2.png) <br>

* ___4.2. Утилита nmap___
    * Командой ping найди машину, которая не «пингуется», после чего утилитой nmap покажи, что хост машины запущен. <br>
    * Не пингуется вторая машина. Особенностью nmap является то, что эта утилита может определить, включен ли хост, даже если его нельзя пропинговать.<br>
    * Проверяемm включен ли хост второй машины через первую: <br>
    * Хост второй машины, которая не пингуется, включен: <br>
    ![alt text](../misc/images_for_report/nmapcheckhost.png)

## Part 5. Статическая маршрутизация сети
__Сеть__: <br>
![part5_network](../misc/images/part5_network.png)
* Поднятие машин: <br>
![alt text](../misc/images_for_report/createvms.png)
> Чтобы работать одновременно во внутренней сети и при этом скачивать утилиты из Интернета, нужно включить в настройках второй адаптер: <br>
![alt text](../misc/images_for_report/secondAdapter.png)
* ___5.1. Настройка адресов машин___
    * __ws11__:
        * Выставляем в параметре адаптер1: внутрення сеть, имя intnet1:
        ![alt text](../misc/images_for_report/ws11configWM.png)
        * Изменение конфигурационного файла: <br>
        ![alt text](../misc/images_for_report/changeconfigws11.png)

    * __ws21__:
        * Выставляем в параметре адаптер1: внутрення сеть, имя intnet3:
        ![alt text](../misc/images_for_report/ws21configWM.png)
        * Изменение конфигурационного файла: <br>
        ![alt text](../misc/images_for_report/changeconfigws21.png)

    * __ws22__:
        * Выставляем в параметре адаптер1: внутрення сеть, имя intnet3:
        ![alt text](../misc/images_for_report/ws21configWM.png)
        * Изменение конфигурационного файла: <br>
        ![alt text](../misc/images_for_report/changeconfigws22.png)

    * __r1__:
        * Выставляем в параметрах адаптер2 и адаптер3: внутренняя сеть, имена intnet1 и intnet2: <br>
        ![alt text](../misc/images_for_report/r1configVM.png)
        * Изменение конфигурационного файла: <br>
        ![alt text](../misc/images_for_report/changeconfigr1.png)

    * __r2__:
        * Выставляем в параметрах адаптер2 и адаптер3: внутренняя сеть, имена intnet2 и intnet3: <br>
        ![alt text](../misc/images_for_report/r2configVM.png)
        * Изменение конфигурационного файла: <br>
        ![alt text](../misc/images_for_report/changeconfigr2.png)

    * Перезапускаем сервис сети с помощью команды: `sudo netplan apply` <br>
    * Проверяем адрес машин с помощью: `ip -4 a` <br>
        * ws11: <br>
        ![alt text](../misc/images_for_report/checkaddressws11.png)
        * ws21: <br>
        ![alt text](../misc/images_for_report/checkaddressws21.png)
        * ws22: <br>
        ![alt text](../misc/images_for_report/checkaddressws22.png)
        * r1: <br>
        ![alt text](../misc/images_for_report/checkaddressr1.png)
        * r2: <br>
        ![alt text](../misc/images_for_report/checkaddressr2.png)
    
    * Пингуем ws22 с ws21: <br>
    ![alt text](../misc/images_for_report/pingws22withws21.png)
    * Пингуем r1 с ws11: <br>
    ![alt text](../misc/images_for_report/pingr1withws11.png)

* ___5.2. Включение переадресации IP-адресов___
    * Для включения переадресации IP, выполни команду на роутерах: `sysctl -w net.ipv4.ip_forward=1`. При таком подходе переадресация не будет работать после перезагрузки системы.
        * r1: <br>
        ![alt text](../misc/images_for_report/changeaddressr152.png)
        * r2: <br>
        ![alt text](../misc/images_for_report/changeaddressr252.png)
    * Открой файл /etc/sysctl.conf и добавь в него следующую строку: `net.ipv4.ip_forward = 1`. При использовании этого подхода, IP-переадресация включена на постоянной основе.
        * r1 и r2: <br>
        ![alt text](../misc/images_for_report/changeaddressr1perm.png)

* ___5.3. Установка маршрута по-умолчанию___
    * Настрой маршрут по-умолчанию (шлюз) для рабочих станций. Для этого добавь default перед IP роутера в файле конфигураций. <br>
        * ws11: <br>
        ![alt text](../misc/images_for_report/ws153.png)
        * ws21: <br>
        ![alt text](../misc/images_for_report/ws2153.png)
        * ws22: <br>
        ![alt text](../misc/images_for_report/ws2253.png)
    
    * Вызови ip r и покажи, что добавился маршрут в таблицу маршрутизации. <br>
        * ws11: <br>
        ![alt text](../misc/images_for_report/ws1153cipr.png)
        * ws21: <br>
        ![alt text](../misc/images_for_report/ws2153ipr.png)
        * ws22: <br>
        ![alt text](../misc/images_for_report/ws2253ipr.png)

    * Пропингуй с ws11 роутер r2 и покажи на r2, что пинг доходит. Для этого используй команду: `tcpdump -tn -i eth0`<br>
        * ws11: <br>
        ![alt text](../misc/images_for_report/ws11tcpdump53.png)
        * r2: <br>
        ![alt text](../misc/images_for_report/r2tcpdump53.png)

* ___5.4. Добавление статических маршрутов___
    1. Добавь в роутеры r1 и r2 статические маршруты в файле конфигураций. <br>
        * r1: <br>
        ![alt text](../misc/images_for_report/541r1.png)
        * r2: <br>
        ![alt text](../misc/images_for_report/541r2.png)
    2. Вызови ip r и покажи таблицы с маршрутами на обоих роутерах. <br>
        * r1: <br>
        ![alt text](../misc/images_for_report/542r1.png)
        * r2: <br>
        ![alt text](../misc/images_for_report/542r2.png)
    3. Запусти команды на ws11: `ip r list 10.10.0.0/[маска сети]` и `ip r list 0.0.0.0/0` <br>
    ![alt text](../misc/images_for_report/543.png)
        * ws11 соединена с сетью 10.10.0.0/18 по своему IP-адресу 10.10.0.2, для других адресов используется маршрут по умолчанию.  Был выбран такой маршрут потому что он обладает большей точностью.

* ___5.5. Построение списка маршрутизаторов___
    ![alt text](../misc/images_for_report/55tcpdump.png)
    * При помощи утилиты traceroute построй список маршрутизаторов на пути от ws11 до ws21. <br>
    ![alt text](../misc/images_for_report/55traceroute.png)
    * При этом на r1 отображаются дополнительные данные. <br>
    * Traceroute — это утилита, которая позволяет проследить маршрут следования данных до удаленного адресата в сетях TCP/IP. В Linux используется команда Traceroute. Утилита Traceroute формирует UDP-датаграмму (сообщение, которое нужно доставить целевому серверу), упаковывает ее в IP-пакет и передаёт первому транзитному узлу. В заголовке такого IP-пакета есть поле TTL (Time To Live) — время жизни пакета. Оно определяет количество хопов, через которые пакет может пройти. На каждом узле TTL уменьшается на единицу. Если на пути к удаленному адресату время жизни пакета станет равно 0, маршрутизатор отбросит пакет и отправит источнику ICMP-сообщение об ошибке «Time Exceeded» (время истекло).

* ___5.6. Использование протокола ICMP при маршрутизации___
    1. Запусти на r1 перехват сетевого трафика, проходящего через eth0 с помощью команды: `tcpdump -n -i eth0 icmp`<br>
    ![alt text](../misc/images_for_report/561.png)
    2. Пропингуй с ws11 несуществующий IP (например, 10.30.0.111) с помощью команды: `ping -c 1 10.30.0.111` <br>
    ![alt text](../misc/images_for_report/562.png)

## Part 6. Динамическая настройка IP с помощью DHCP

* __6.1. Укажи адрес маршрутизатора по-умолчанию, DNS-сервер и адрес внутренней сети. Пример файла для r2:__
    * Устанавливаем isc-dhcp-server, далее меняем конфигурационный файл: <br>
    ![alt text](../misc/images_for_report/61.png)

* __6.2. В файле resolv.conf пропиши nameserver 8.8.8.8.__ <br>
    ![alt text](../misc/images_for_report/62.png)

* __6.3. Перезагрузи службу DHCP командой systemctl restart isc-dhcp-server. Машину ws21 перезагрузи при помощи reboot и через ip a покажи, что она получила адрес. Также пропингуй ws22 с ws21.__ <br>
    1. Рестарт службы <br> 
    ![alt text](../misc/images_for_report/631.png)
    2. Меняем конфигурационные файлы, чтобы разрешить службу dhcp4: <br>
    ![alt text](../misc/images_for_report/image-9.png)
    ![alt text](../misc/images_for_report/image-10.png)
    3. Резуельтат `ip a`: <br>
        * ws21: <br>
        ![alt text](../misc/images_for_report/image-11.png)
        * ws22: <br>
        ![alt text](../misc/images_for_report/image-12.png)
    4. Пинг ws22 с ws21: <br>
        ![alt text](../misc/images_for_report/image-13.png)

* __6.4. Укажи MAC адрес у ws11, для этого в etc/netplan/00-installer-config.yaml надо добавить строки: macaddress: 10:10:10:10:10:BA, dhcp4: true.__
    1. Изменение конфигурационного файла: <br>
    ![alt text](../misc/images_for_report/image-14.png)
    
* Аналогично для r1: <br>
    1. Установить `isc-dhcp-server` 
    2. Изменить `/etc/dhcp/dhcpd.conf` <br>
    ![alt text](../misc/images_for_report/64_2.png)
    3. Изменить `/etc/resolv.conf`: <br>
    ![alt text](../misc/images_for_report/image-15.png)
    ![alt text](../misc/images_for_report/image-8.png)
    4. Резуельтат `ip a` для ws11: <br>
    ![alt text](../misc/images_for_report/image-16.png)
    5. Пинг: <br>
    ![alt text](../misc/images_for_report/image-17.png)
    
* __6.5. Запроси с ws21 обновление ip адреса.__
    1. IP до обновления: <br>
    ![alt text](../misc/images_for_report/image-18.png)
    2. Обновление с помощью: `sudo dhclient`: <br>
    ![alt text](../misc/images_for_report/image-19.png)
    3. Удаление старого ip: `sudo dhclient -r`<br>
    ![alt text](../misc/images_for_report/image-20.png)
    4. Для удаления или обновления кокретного сетевого интерфейса добавляют в конце имя интерфейса. `sudo dhclient -r eth0`
    ![alt text](../misc/images_for_report/image-21.png)
    * Были использованы следующие опции DHCP протокола:
    option routers ip-address [, ip-address...]; - адреса шлюзов для клиентской сети. <br>
    option domain-name-servers ip-address [, ip-address...]; - Список DNS серверов доступных клиенту. Сервера должны быть перечислены в порядке предпочтительности.

## Part 7. NAT

* Устанавливаем apache2: `sudo apt install apache2`
* __7.1. В файле /etc/apache2/ports.conf на ws22 и r1 измени строку Listen 80 на Listen 0.0.0.0:80, то есть сделай сервер Apache2 общедоступным.__ <br>
    * r1: <br>
    ![alt text](../misc/images_for_report/image-22.png)
    * ws22: <br>

* __7.2. Запусти веб-сервер Apache командой service apache2 start на ws22 и r1.__
    * r1: <br>
    ![alt text](../misc/images_for_report/image-23.png)
    * ws22: <br>

* __7.3. Добавь в фаервол, созданный по аналогии с фаерволом из Части 4, на r2 следующие правила:__
    1) Удаление правил в таблице filter - iptables -F; <br>
    2) Удаление правил в таблице "NAT" - iptables -F -t nat; <br>
    3) Отбрасывать все маршрутизируемые пакеты - iptables --policy FORWARD DROP. <br>
    ![alt text](../misc/images_for_report/image-24.png)
    4) Запусти файл также, как в Части 4. <br>
    ![alt text](../misc/images_for_report/image-29.png) <br>
    Пинг: <br>
    ![alt text](../misc/images_for_report/image-26.png)
    ![alt text](../misc/images_for_report/image-27.png)
* __7.4. Добавь в файл ещё одно правило:__
    4) Разрешить маршрутизацию всех пакетов протокола ICMP. <br>
    ![alt text](../misc/images_for_report/image-28.png)

* __7.5. Включи SNAT, а именно маскирование всех локальных ip из локальной сети, находящейся за r2 (по обозначениям из Части 5 - сеть 10.20.0.0).Включи DNAT на 8080 порт машины r2 и добавить к веб-серверу Apache, запущенному на ws22, доступ извне сети. __
    ![alt text](../misc/images_for_report/image-30.png)



