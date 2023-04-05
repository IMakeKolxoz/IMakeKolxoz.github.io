# Установка времени и даты
 ```
/system clock set time=10:30:00 date=2023-04-06 time-zone=Asia/Chita
 /system backup save
 ```
 # Задача DNS
 ```
 /ip dns set primary-dns=77.88.8.8 secondary-dns=77.88.8.1
 ```
  # Задача статического айпи
1. Откройте консольный интерфейс роутера MikroTik в EVE-NG.

2. Введите команду `ip address print`, чтобы просмотреть текущие настройки IP-адреса роутера.

3. Выберите интерфейс, на который вы хотите назначить статический IP-адрес. Обычно это будет интерфейс ether1, который подключен к локальной сети EVE-NG.

4. Введите команду ```ip address add address=[IP-адрес/маска] interface=[имя интерфейса]```, чтобы назначить статический IP-адрес на выбранный интерфейс. Например, если вы хотите назначить IP-адрес 192.168.1.2 с маской 24 на интерфейс ether1, то введите следующую команду:
   ```
   /ip address add address=192.168.1.2/24 interface=ether1
   ```
5. Введите команду ip address print, чтобы убедиться, что IP-адрес был успешно назначен.

6. Настройте статический IP-адрес на других устройствах в вашей сети, указав IP-адрес роутера MikroTik как шлюз по умолчанию. Для этого настройте IP-адрес на устройстве вручную, используя соответствующую утилиту на ОС вашего устройства.

7. Проверьте соединение с другими устройствами в вашей сети, чтобы убедиться, что статический IP-адрес был успешно назначен и работает правильно.

После выполнения этих шагов устройство на роутере MikroTik в EVE-NG будет иметь статический IP-адрес.

# Настройка выхода в интернет 
1. Настройте интерфейс, подключенный к интернету (обычно это интерфейс, подключенный к модему или провайдерской сети). Для этого введите команду /ip address add address=IP_адрес/маска интерфейс, где вместо IP_адрес/маска следует указать соответствующий IP-адрес и маску сети интерфейса, а вместо интерфейс - название интерфейса. Например:
   ```
    /ip address add address=192.168.1.100/24 interface=ether1
   ```
2. Настройте NAT, чтобы локальные устройства могли выходить в интернет. Для этого введите команду /ip firewall nat add chain=srcnat out-interface=интерфейс action=masquerade, где вместо интерфейс следует указать название интерфейса, подключенного к интернету. Например:
   ```
    /ip firewall nat add chain=srcnat out-interface=интерфейс action=masquerade
   ```
3. Настройте DNS-серверы. Вы можете использовать публичные DNS-серверы, например, от yandex (77.88.8.8 и 77.88.8.1). Для этого введите команду 
    ```
    /ip dns set servers=77.88.8.8,77.88.8.1
    ```
4. Сохраните настройки командой `/system backup save`, чтобы сохранить настройки роутера.
   
# Задача доменного имени 
Чтобы настроить доменное имя для устройств в одной сети в топологии в EVE-NG с роутерами MikroTik, вам нужно выполнить следующие шаги:
1. Создайте DNS-сервер на роутере MikroTik. Для этого введите команду `/ip dns set allow-remote-requests=yes`, чтобы разрешить удаленные запросы, и /`ip dns static add name=имя_устройства address=IP_адрес`, чтобы создать запись о статическом IP-адресе устройства и его имени. Например:
   ```
   /ip dns set allow-remote-requests=yes
   /ip dns static add name=server1.local address=192.168.1.100
   ```
2. Настройте DHCP-сервер на роутере MikroTik, чтобы устройства, подключенные к сети, получали IP-адреса и DNS-сервер автоматически. Для этого введите команду /ip dhcp-server setup, чтобы запустить мастер настройки DHCP-сервера, и следуйте инструкциям. Выберите интерфейс, на котором будет работать DHCP-сервер, настройте IP-адрес пула, указав диапазон адресов, которые будут выдаваться клиентам, и задайте DNS-сервер, указав IP-адрес DNS-сервера, созданного на предыдущем шаге.
3. Настройте каждое устройство в сети, чтобы оно использовало созданный DNS-сервер. Для этого введите команду /ip dns set servers=IP_адрес_DNS_сервера, где вместо IP_адрес_DNS_сервера следует указать IP-адрес DNS-сервера, созданного на предыдущем шаге.
   
# Настройка DHCP
   Для настройки DHCP сервера на роутере Mikrotik в EVE-NG выполните следующие шаги:
   1. Зайдите в консоль роутера Mikrotik в EVE-NG.
   2. Войдите в режим конфигурации командой /ip dhcp-server
   3. Задайте сеть и маску подсети, на которой будет работать DHCP сервер, командой `/ip dhcp-server network add address=<IP адрес сети> netmask=<маска подсети> gateway=<IP адрес шлюза>`
   4. Настройте пул адресов, который будет выдавать DHCP сервер, командой `/ip dhcp-server add address-pool=<название пула> ranges=<начальный IP адрес>-<конечный IP адрес>`
   5. Включите DHCP сервер командой `/ip dhcp-server enable`
   6. Проверьте настройки DHCP сервера командой `/ip dhcp-server print`
   
Например, если необходимо настроить DHCP сервер на сети 192.168.1.0/24 для выдачи адресов в диапазоне от 192.168.1.100 до 192.168.1.200, то команды будут выглядеть следующим образом:
```
/ip dhcp-server network add address=192.168.1.0/24 gateway=192.168.1.1
/ip dhcp-server add address-pool=dhcp-pool ranges=192.168.1.100-192.168.1.200
/ip dhcp-server enable
/ip dhcp-server print
```
После настройки DHCP сервера, устройства в сети должны автоматически получать IP адрес от роутера Mikrotik.

# Получение автоматического  IP-адреса через DHCP 
На устройстве с Ubuntu также может быть выполнена через командную строку. Для этого выполните следующие команды:
1. Откройте терминал на устройстве Ubuntu.
2. Введите команду `sudo nano /etc/netplan/01-network-manager-all.yaml` для редактирования файла настроек сети.
3. Найдите раздел для соединения, которое вы хотите настроить на автоматическое получение IP-адреса, и добавьте строку `dhcp4: true` в этот раздел, например:
   ```
    network:
    version: 2
    renderer: networkd
    ethernets:
        enp0s3:
        dhcp4: true
   ```
4. Сохраните изменения и закройте файл настроек. (CTRL+X; Y; ENTER)
5. Выполните команду `sudo netplan apply` для применения изменений в настройках сети.
   
# Настройка синхронизации времени
Для настройки синхронизации времени на устройствах в Ubuntu, вы можете использовать сервис NTP (Network Time Protocol). Следуя этим шагам, вы сможете настроить синхронизацию времени на вашем устройстве:
1. Установите пакет NTP с помощью команды:
   ```
    sudo apt-get install ntp
   ```
2. Отредактируйте файл конфигурации NTP, открыв его в текстовом редакторе, например, nano:
   ```
    sudo nano /etc/ntp.conf
   ```
3. В файле конфигурации укажите сервера NTP, с которых вы хотите получать время. Это можно сделать, добавив или изменив строки, начинающиеся с "server". Например, для использования сервера NTP от Google, добавьте следующую строку:
   ```
    server 0.google.pool.ntp.org
   ```
4. Сохраните изменения в файле конфигурации и закройте его. `(CTRL+X; Y; ENTER)`

5. Запустите сервис NTP, используя команду:
   ```
    sudo service ntp start
   ``` 
6. Для автоматической синхронизации времени при каждой загрузке устройства, добавьте сервис NTP в автозагрузку:
   ```
    sudo update-rc.d ntp defaults
   ``` 
#  Установка minecraft-server(не докер, докер в конце)
Для установки сервера Minecraft на Ubuntu в EVE-NG, следуйте этим шагам:
1. Откройте терминал в Ubuntu и выполните обновление пакетов:
    ```
    sudo apt update
    sudo apt upgrade
    ```
2. Установите Java:
    ```
    sudo apt install openjdk-8-jre-headless
    ```
3. Скачайте сервер Minecraft с официального сайта:
    ```
    wget https://launcher.mojang.com/v1/objects/1c1b4b586b4a76d99c99c00cc9d69142b92d43e7/server.jar
    ```
4. Создайте директорию, в которой будет храниться сервер Minecraft:
    ```
    mkdir minecraft_server
    cd minecraft_server
    ```
5. Запустите сервер Minecraft:
    ```
    java -Xmx1024M -Xms1024M -jar server.jar nogui
    ```
6. После запуска сервера, он создаст несколько файлов, включая файл конфигурации `server.properties.`
7. Настройте конфигурацию сервера, если это необходимо, в файле `server.properties`
```
 motd=Hello world
 server-port=25565
 max-players=20
 spawn-protection=16
 view-distance=10
 gamemode=survival
 difficulty=easy
 level-seed=
 enable-command-block=false
```
8. Для запуска сервера Minecraft как службы, создайте файл `minecraft.service` в каталоге `/etc/systemd/system/`:
    ```
    sudo nano /etc/systemd/system/minecraft.service
    ```
9.  Вставьте следующий текст:
    ```
    [Unit]
    Description=Minecraft Server
    After=network.target

    [Service]
    User=<USERNAME>
    Nice=1
    KillMode=none
    SuccessExitStatus=0 1
    ProtectHome=true
    ProtectSystem=full
    PrivateDevices=true
    NoNewPrivileges=true
    WorkingDirectory=/home/<USERNAME>/minecraft_server
    ExecStart=/usr/bin/java -Xmx1024M -Xms1024M -jar server.jar nogui
    ExecStop=/usr/bin/screen -p 0 -S minecraft-server -X eval 'stuff "say SERVER SHUTTING DOWN. Saving map..."\\015'
    ExecStop=/bin/sleep 10
    ExecStop=/usr/bin/screen -p 0 -S minecraft-server -X eval 'stuff "save-all"\\015'
    ExecStop=/usr/bin/screen -p 0 -S minecraft-server -X eval 'stuff "stop"\\015'
    ExecStop=/bin/sleep 10

    [Install]
    WantedBy=multi-user.target
    ```
    Замените `<USERNAME>` на имя вашего пользователя.

10. Сохраните файл`minecraft.service` и закройте его.`(CTRL+X; Y; ENTER)`
11. Обновите список служб systemd:
    ```
    sudo systemctl daemon-reload
    ```
12. Запустите службу Minecraft:
    ```
    sudo systemctl start minecraft.service
    ```
13. Проверьте статус службы Minecraft:
    ```
    sudo systemctl status minecraft.service
    ```
    Если все настроено правильно, вы должны увидеть, что служба работает без ошибок.
14. Для запуска службы при загрузке системы Ubuntu, выполните следующую команду:
    ```
    sudo systemctl enable minecraft.service
    ```
    Теперь вы можете подключаться к серверу Minecraft, используя IP-адрес вашего устройства и порт, который вы настроили в файле `server`

# Установка minecraft-client 
команды для установки Minecraft клиента на Ubuntu:
1. Скачайте Minecraft клиент с официального сайта: 
   ```
    cd ~/Downloads
    wget https://launcher.mojang.com/download/Minecraft.tar.gz
   ```
2. Распакуйте загруженный архив в удобное место на вашем компьютере:
   ```
   tar -zxvf Minecraft.tar.gz
   ```
3. Установите зависимости для Minecraft:
   ```
    sudo apt update
    sudo apt install default-jre
   ```
4. Запустите Minecraft: 
   ```
    cd ~/Downloads/Minecraft
    java -jar minecraft.jar
   ```
# Установка майнкрафт сервер ДОКЕР
Установка Minecraft сервера с помощью Docker достаточно проста. Вам потребуется установить Docker на вашу систему. Если Docker уже установлен, пропустите первый шаг.
1. Установка Docker
   Для установки Docker на Ubuntu используйте следующие команды:
   ```
    sudo apt update
    sudo apt install docker.io
   ```
   После установки Docker необходимо запустить сервис:
    ```
    sudo systemctl start docker
    ```
2. Создание контейнера
   Чтобы создать контейнер с Minecraft сервером, выполните следующую команду:
   ```
   sudo docker run -d -p 25565:25565 --name minecraft-server itzg/minecraft-server
   ```
   Эта команда загрузит образ Minecraft сервера с Docker Hub и создаст контейнер с именем minecraft-server. Опция -d означает, что контейнер должен быть запущен в фоновом режиме. Опция -p привязывает порт 25565 контейнера к порту 25565 на хосте.

    Вы можете использовать опцию -e, чтобы задать некоторые настройки сервера Minecraft, например, количество максимальных игроков, название сервера и т.д. Например:
    ```
    sudo docker run -d -p 25565:25565 --name minecraft-server -e EULA=TRUE -e MAX_PLAYERS=12 -e MOTD="My Minecraft Server" itzg/minecraft-server
    ```
3. Проверка работы сервера
   Чтобы проверить, что сервер запущен, выполните команду:
   ```
   sudo docker ps
   ```
    Вы увидите список всех запущенных контейнеров Docker, включая minecraft-server.
    Теперь вы можете подключиться к вашему Minecraft серверу, используя его IP-адрес и порт 25565.
4. Создание директории для сохранения мира
   Для создания директории на хосте можно воспользоваться командой `mkdir`. Например, для создания директории `minecraft-worlds` на рабочем столе в Ubuntu можно выполнить следующую команду в терминале:
   ```
   mkdir ~/Desktop/minecraft-worlds
   ```
   Здесь `~` обозначает домашнюю директорию пользователя, а `Desktop/minecraft-worlds` — путь к создаваемой директории относительно домашней директории.
5. Сохранение мира Minecraft
   Мир Minecraft будет сохраняться внутри контейнера Docker. Чтобы сохранить мир на хосте, вы можете смонтировать директорию на хосте внутри контейнера. Для этого используйте опцию -v:
   ```
    sudo docker run -d -it -p 25565:25565 --name minecraft-server -v ~/Desktop/minecraft-worlds:/data itzg/minecraft-server
   ```
   Замените /path/on/host на путь к директории на хосте, в которую вы хотите сохранять мир Minecraft.

    Вот и все! Теперь вы можете наслаждаться своим Minecraft сервером, работающим в контейнере Docker.

# Разворачивание WEB-servera 
Для развертывания веб-сервера на виртуальной машине на Ubuntu в EVE-NG нужно выполнить следующие шаги:

1. Установить веб-сервер Apache:
   ```
   sudo apt update
   sudo apt install apache2
   ```
2. Проверить, что Apache запущен:
   ```
   sudo systemctl status apache2
    ```
3. Установить PHP, если его нет:
   ```
   sudo apt install php libapache2-mod-php
   ```
4. Создать тестовый файл PHP для проверки работы сервера:
   ```
   sudo nano /var/www/html/info.php
   ```   
   В открывшемся редакторе вставить следующий код:
   ```
    <?php
    phpinfo();
    ?>
   ``` 
   Сохранить и закрыть файл. `(CTRL+X; Y; ENTER)`
5. Проверить работу веб-сервера, открыв в браузере страницу:
   ```
    http://IP-адрес-виртуальной-машины/info.php
   ```
    Где вместо "IP-адрес-виртуальной-машины" нужно указать IP-адрес виртуальной машины в EVE-NG.

    После выполнения этих шагов веб-сервер будет развернут на виртуальной машине на Ubuntu в EVE-NG.