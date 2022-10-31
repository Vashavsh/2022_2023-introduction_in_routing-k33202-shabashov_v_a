#### University: [ITMO University](https://itmo.ru/ru/)
##### Faculty: [FICT](https://fict.itmo.ru)
##### Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)
##### University: [ITMO University](https://itmo.ru/ru/)
##### Faculty: [FICT](https://fict.itmo.ru)
##### Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)
Year: 2022/2023

Group: K33202

Author: Davydov Anton Dmitrievich

Lab: Lab1

Date of create: 10.09.2022

Date of finished: 21.10.2022

## Отчёт по лабораторной работе №1 "Установка ContainerLab и развертывание тестовой сети связи"

**Цель работы:** ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т. д.

**Артефакты выполнения работы:**

1. Текст файла для развертывания тестовой сети с расширением .yaml

```
name: lab1
mgmt:
  network: statics
  ipv4_subnet: 172.20.20.0/24

topology: 

  nodes:
    R01.TEST: 
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.2

    SW01.L3.01.TEST:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.3

    SW02.L3.01.TEST:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.4

    SW02.L3.02.TEST:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.5

    PC1:
      kind: linux
      image: alpine:latest
      mgmt_ipv4: 172.20.20.6

    PC2:
      kind: linux
      image: ubuntu:latest
      mgmt_ipv4: 172.20.20.7

  links: 
    - endpoints: ["R01.TEST:eth1","SW01.L3.01.TEST:eth1"] 
    - endpoints: ["SW01.L3.01.TEST:eth2","SW02.L3.01.TEST:eth1"] 
    - endpoints: ["SW01.L3.01.TEST:eth3","SW02.L3.02.TEST:eth1"] 
    - endpoints: ["SW02.L3.01.TEST:eth2","PC1:eth1"] 
    - endpoints: ["SW02.L3.02.TEST:eth2","PC2:eth1"]
```
2. Схема связи

![](https://github.com/Antoshik143/2022_2023-introduction_in_routing-k33202-davydov_a_d/blob/main/lab1/draw.png "Схема сети")

3. Тексты конфигураций для сетевых устройств
 * Роутер
```
# oct/09/2022 14:34:28 by RouterOS 6.47.9
# software id = 
#
#
#
/interface vlan
add interface=ether2 name=vlan10 vlan-id=10
add interface=ether2 name=vlan20 vlan-id=20
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool10 ranges=192.168.10.10-192.168.10.254
add name=pool20 ranges=192.168.20.10-192.168.20.254
/ip dhcp-server
add address-pool=pool10 disabled=no interface=vlan10 name=dhcp10
add address-pool=pool20 disabled=no interface=vlan20 name=dhcp20
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=192.168.10.1/24 interface=vlan10 network=192.168.10.0
add address=192.168.20.1/24 interface=vlan20 network=192.168.20.0
/ip dhcp-client
add disabled=no interface=ether1
/ip dhcp-server network
add address=192.168.10.0/24 gateway=192.168.10.1
add address=192.168.20.0/24 gateway=192.168.20.1
/system identity
set name=R01.TEST
```

* Свитч 1 уровня
```
# oct/09/2022 14:33:43 by RouterOS 6.47.9
# software id = 
#
#
#
/interface bridge
add name=bridge10
add name=bridge20
/interface vlan
add interface=ether2 name=vlan10 vlan-id=10
add interface=ether2 name=vlan20 vlan-id=20
add interface=ether3 name=vlan100 vlan-id=10
add interface=ether4 name=vlan200 vlan-id=20
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port
add bridge=bridge10 interface=vlan10
add bridge=bridge20 interface=vlan20
add bridge=bridge10 interface=vlan100
add bridge=bridge20 interface=vlan200
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=bridge10
add disabled=no interface=bridge20
/system identity
set name=SW01.L3.01.TEST
```

* Первый свитч 2 уровня
```
# oct/09/2022 18:09:23 by RouterOS 6.47.9
# software id = 
#
#
#
/interface bridge
add name=bridge10
/interface vlan
add interface=ether2 name=vlan10 vlan-id=10
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port
add bridge=bridge10 interface=vlan10
add bridge=bridge10 interface=ether3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=bridge10
/system identity
set name=SW02.L3.01.TEST
```
* Второй свитч 2 уровня
```
# oct/09/2022 18:14:27 by RouterOS 6.47.9
# software id = 
#
#
#
/interface bridge
add name=bridge20
/interface vlan
add interface=ether2 name=vlan20 vlan-id=20
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port
add bridge=bridge20 interface=vlan20
add bridge=bridge20 interface=ether3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=bridge20
/system identity
set name=SW02.L3.02.TEST
```
4. Результаты пингов для проверки целостности локальной сети

![](https://github.com/Antoshik143/2022_2023-introduction_in_routing-k33202-davydov_a_d/blob/main/lab1/Ping1.png "Проверка локальной сети и пингов с роутера")
![](https://github.com/Antoshik143/2022_2023-introduction_in_routing-k33202-davydov_a_d/blob/main/lab1/Ping2.png "Проверка пингов со свитча 2 уровня")

**Вывод:**
<<<<<<< HEAD
В ходе работы мы 
=======
В ходе работы мы познакомились с containerlab - средством для создания сетевых лаб на основе контейнеров. С помощью файла конфигурации сети yaml мы создали сеть, состоящую из 3 коммутаторов, 2 ПК и маршуртизатора, которую развернули, используя методы containerlab в виде самостоятельных контейнеров. Также в сети мы настроили 
2 vlan'а, а также dhcp-сервер для автоматического получения IP-адресов. Для тестирования работоспособности сети мы сделали проверочные пинги внутри всех vlan'ов.
>>>>>>> 86936598cd3e2e2e7cdd792160389a98f1291e61
