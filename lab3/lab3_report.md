University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33202

Author: Davydov Anton Dmitrievich

Lab: Lab3

Date of create: 29.11.2022

Date of finished: 02.12.2022

## Отчёт по лабораторной работе №3 "Эмуляция распределенной корпоративной сети связи, настройка OSPF и MPLS, организация первого EoMPLS"

**Цель работы:** изучить протоколы OSPF и MPLS, механизмы организации EoMPLS. 

**Артефакты выполнения работы:**

1. Текст файла для развертывания тестовой сети с расширением .yaml
```
name: lab3

mgmt:
  network: statics
  ipv4_subnet: 172.20.20.0/24

topology:

  nodes:
    R01.MSK:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.2

    R01.SPB:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.3

    R01.HKI:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.4

    R01.LND:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.5

    R01.NY:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.6

    R01.LBN:
      kind: vr-mikrotik_ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.7

    PC1:
      kind: linux
      image: alpine:latest
      mgmt_ipv4: 172.20.20.8

    SGI.Prism:
      kind: linux
      image: alpine:latest
      mgmt_ipv4: 172.20.20.9
  

  links:
    - endpoints: ["R01.MSK:eth1","R01.SPB:eth1"]
    - endpoints: ["R01.MSK:eth2","R01.LBN:eth1"]
    - endpoints: ["R01.SPB:eth2","PC1:eth1"]
    - endpoints: ["R01.SPB:eth3","R01.HKI:eth1"]
    - endpoints: ["R01.HKI:eth2","R01.LBN:eth2"]
    - endpoints: ["R01.HKI:eth3","R01.LND:eth1"]
    - endpoints: ["R01.LND:eth2","R01.NY:eth1"]
    - endpoints: ["R01.LBN:eth3","R01.NY:eth2"]
    - endpoints: ["R01.NY:eth3","SGI.Prism:eth1"]
```

2. Схема связи

![](https://github.com/Antoshik143/2022_2023-introduction_in_routing-k33202-davydov_a_d/blob/main/lab3/Схема.png "Схема сети")

3. Тексты конфигураций для сетевых устройств
