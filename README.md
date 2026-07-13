# ~~Awesome~~ Roadmap ESP32

[ESP32](https://ru.wikipedia.org/wiki/ESP32) — серия микропроцессоров с малым энергопотреблением китайской компании [Espressif Systems](https://www.espressif.com/).
ESP32 создан и разработан компанией, расположенной в Шанхае, а производится компанией TSMC по техпроцессу 40 нм и 28 нм. Серия является преемником микросхем ESP8266.

## Содержание

[Прочти меня](#прочти-меня) 

[Решения](#решения)

[Аппаратное обеспечение](#аппаратное-обеспечение)

[ESP8286](#esp8286)

[ESP32](#esp32)

[ESP32-H](#esp32-h)

[ESP32-C](#esp32-c)

[ESP32-S](#esp32-s)

[ESP32-E](#esp32-e)

[ESP32-P](#esp32-p)

[Программное обеспечение](#программное-обеспечение)

[Проекты](#проекты)

[Публикации](#публикации)

[Глоссарий](#глоссарий)

## Решения
| Решение                                                              | Сайт                                                                                         | Докуметация                                                                                                                     | Код                                                  |
|----------------------------------------------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------|
| [Artificial intelligence](#artificial-intelligence)                  |                                                                                              |                                                                                                                                 |                                                      |
| [AT application](#at-application)                                    |                                                                                              |                                                                                                                                 |                                                      |
| [Audio development framework](#audio-development-framework)          |                                                                                              |                                                                                                                                 |                                                      |
| [BLE Mesh development framework](#ble-mesh-development-framework)    | [BLE Mesh](https://www.espressif.com/en/products/sdks/esp-idf/esp-ble-mesh)                  | [Docs](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-guides/esp-ble-mesh/ble-mesh-index.html#getting-started) |                                                      |
| [Camera application](#camera-application)                            |                                                                                              |                                                                                                                                 |                                                      |
| [ESP Matter](#esp-matter)                                            | [ESP Matter](https://www.espressif.com/en/solutions/device-connectivity/esp-matter-solution) | [Docs](https://docs.espressif.com/projects/esp-matter/en/latest/esp32/)                                                         | [GitHub](https://github.com/espressif/esp-matter)    |
| [ESP-NOW](#esp-now)                                                  | [ESP-NOW](https://www.espressif.com/en/solutions/low-power-solutions/esp-now)                | [Docs](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/api-reference/network/esp_now.html)                        | [GitHub](https://github.com/espressif/esp-now)       |
| [ESP RainMaker cloud service](#esp-rainmaker-cloud-service)          |                                                                                              |                                                                                                                                 |                                                      |
| [Third party cloud service](#third-party-cloud-service)              |                                                                                              |                                                                                                                                 |                                                      |
| [Wi-Fi Mesh development framework](#wi-fi-mesh-development-framewor) | [Wi-Fi Mesh](https://www.espressif.com/en/products/sdks/esp-wifi-mesh/overview)              | [Docs](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/esp-wifi-mesh.html)                               | [GitHub](https://github.com/espressif/esp-mesh-lite) |

### Artificial intelligence

### AT application

### Audio development framework

### BLE Mesh development framework

### Camera application

### ESP Matter

[Matter](https://ru.wikipedia.org/wiki/Matter_(%D1%81%D1%82%D0%B0%D0%BD%D0%B4%D0%B0%D1%80%D1%82)) - единый стандарт подключения 
для умного дома с открытым исходным кодом, который позволяет объединять IoT-устройства разных производителей в единую сеть.
Стандарт работает на основе интернет-протокола (IP) и функционирует через один или несколько контроллеров (хабов), 
которые соединяют разнородные IoT-устройства в единую сеть и обрабатывают запросы. Продукты, сертифицированные Matter, 
спроектированы для локальной работы, то есть могут работать и без подключения к Интернету. Для гарантированного взаимодействия 
всех Matter-совместимых устройств необходим такой контроллер (хаб), который на аппаратном уровне имеет реализацию одновременно 
Wi-Fi, Bluetooth LE (BLE) и Thread(более 802.15.4 радио) с Bluetooth LE, тем самым контроллер связывает устройства, 
работающие на всех этих трёх технологиях, в единую локальную сеть. 

[CSA](https://csa-iot.org/ru/)

[CSA Matter](https://csa-iot.org/ru/all-solutions/matter/)

[CSA Спецификация](https://csa-iot.org/ru/developer-resource/specifications-download-request/)

[Home Assistant интеграция](https://www.home-assistant.io/integrations/matter/)

**[ESP32](https://www.espressif.com/en/solutions/device-connectivity/esp-matter-solution)**

 - Wi-Fi End Device - модули с поддержкой Wi-Fi, из серии ESP32, ESP32-C и ESP32-S, могут использоваться для создания устройств Wi-Fi, совместимых с Matter.
 - Thread End Device - ESP32-H и модули, поддерживающие IEEE 802.15.4, могут использоваться для создания Matter-совместимых с Thread End устройств.
 - Ethernet End Device - ESP32 и ESP32-S могут использоваться для создания Ethernet контроллеров, совместимых с совместимых с Matter. Устройства серии ESP32-S должны использовать внешний контроллер Ethernet для обеспечения подключения Ethernet.
 - Thread Border Router - объединяя ESP32-H и ESP32-S3 или другие микроконтроллеры, например, ESP32, для подключения сетей Thread к сетям Wi-Fi может быть построен пограничный маршрутизатор Thread.
 - Matter Bridge - объединяя ESP32-H и ESP32-S3 или другие микроконтроллеры, например, ESP32, можно создать мост Matter-Zigbee для объединения сетей, не использующих Matter.

[Open-Source Matter SDK](https://github.com/project-chip/connectedhomeip)

![](/pic/open-source-matter-sdk.png)

[Espressif’s SDK for Matter](https://github.com/espressif/esp-matter)

![](/pic/matter-sdk.png)

[Docs](https://developer.espressif.com/blog/matter/)

ACK SDK for Matter

Модули ESP-ZeroCode основаны на ESP32-C3 (ESP8685), ESP32-C2 (ESP8684) и ESP32-H2ESP32-H2.

### ESP-NOW

### ESP RainMaker cloud service

### Third party cloud service

### Wi-Fi Mesh development framewor

## Аппаратное обеспечение
[DOCS](https://www.espressif.com/en/support/documents/technical-documents)

### [ESP8286](https://www.espressif.com/en/products/socs/esp8266)

 - ESP8266EX
 - ESP8285
 - ESP-WROOM-02D/02U
 - ESP-WROOM-02
 - ESP8266-DevKitC
 - ESP-Launcher

### [ESP32](https://www.espressif.com/en/products/socs/esp32)
 - ESP32-D0WD-V3 
 - ESP32-D0WDR2-V3 
 - ESP32-U4WDH 
 - ESP32-PICO-V3 
 - ESP32-PICO-V3-02 
 - ESP32-PICO-D4 
 - ESP32-WROOM-32E/32UE 
 - ESP32-WROOM-DA 
 - ESP32-WROVER-E/IE 
 - ESP32-MINI-1/1U 
 - ESP32-PICO-MINI-02/02U 
 - ESP32-PICO-V3-ZERO (ACK Module)
 - ESP32-DevKitC 
 - ESP32-DevKitM-1 
 - ESP32-PICO-KIT 
 - ESP32-PICO-V3-ZERO-DevKit (ACK DevKit)
 - ESP32-Vaquita-DSPG 
 - ESP32-LCDKit

### ESP32-H
[H4](https://www.espressif.com/en/products/socs/esp32-h4)
 - ESP32-H4
 - ESP32-H4-WROOM-1

[H21](https://www.espressif.com/en/products/socs/esp32-h21)

[H2](https://www.espressif.com/en/products/socs/esp32-h2)
 - ESP32-H2 
 - ESP32-H2-MINI-1 
 - ESP32-H2-MINI-1U 
 - ESP32-H2-WROOM-02C 
 - ESP32-H2-DevKitM-1 
 - ESP Thread Border Router/Zigbee Gateway

### ESP32-C
[C61](https://www.espressif.com/en/products/socs/esp32-c61)
- ESP32-C61
- ESP32-C61-WROOM-1
- ESP32-C61-DevKitC-1

[C6](https://www.espressif.com/en/products/socs/esp32-c6)
 - ESP32-C6
 - ESP32-C6-WROOM-1 
 - ESP32-C6-WROOM-1U 
 - ESP32-C6-MINI-1/1U 
 - ESP32-C6-DevKitC-1 
 - ESP32-C6-DevKitM-1

[C5](https://www.espressif.com/en/products/socs/esp32-c5)
 - ESP32-C5
 - ESP32-C5-WROOM-1
 - ESP32-C5-DevKitC-1

[C3](https://www.espressif.com/en/products/socs/esp32-c3)
 - ESP32-C3 
 - ESP8685
 - ESP32-C3-MINI-1/1U 
 - ESP32-C3-WROOM-02/02U 
 - ESP8685-WROOM-01 
 - ESP8685-WROOM-03 
 - ESP8685-WROOM-04 
 - ESP8685-WROOM-05 
 - ESP8685-WROOM-06 
 - ESP8685-WROOM-07 
 - ESP32-C3-DevKitM-1 
 - ESP32-C3-DevKitC-02 
 - ESP32-C3-DevKit-RUST-1 
 - ESP32-C3-AWS-ExpressLink-DevKit 
 - ESP32-C3-LCDkit 
 - ESP32-C3-Lyra

[C2](https://www.espressif.com/en/products/socs/esp32-c2)
 - ESP8684
 - ESP8684-MINI-1/1U 
 - ESP8684-WROOM-01C 
 - ESP8684-WROOM-02C/02UC 
 - ESP8684-WROOM-03 
 - ESP8684-WROOM-04C 
 - ESP8684-WROOM-05 
 - ESP8684-WROOM-06C 
 - ESP8684-WROOM-07
 - ESP8684-DevKitM-1

### ESP32-S
[S31](https://www.espressif.com/en/products/socs/esp32-s31)
 - ESP32-S31
 - ESP32-S31-WROOM-1 
 - ESP32-S31-WROOM-3
 - ESP32-S31-Korvo-1 
 - ESP32-S31-Function-Coreboard-1

[S3](https://www.espressif.com/en/products/socs/esp32-s3)
 - ESP32-S3 
 - ESP32-S3-PICO-1
 - ESP32-S3-WROOM-1/1U 
 - ESP32-S3-WROOM-2 
 - ESP32-S3-MINI-1/1U
 - ESP32-S3-DevKitC-1 
 - ESP32-S3-DevKitM-1 
 - ESP32-S3-BOX-3 
 - ESP32-S3-EYE 
 - ESP32-S3-Korvo-1 
 - ESP32-S3-Korvo-2 
 - ESP32-S3-LCD-EV-Board 
 - ESP32-S3-USB-OTG 
 - ESP32-S3-USB-Bridge

[S2](https://www.espressif.com/en/products/socs/esp32-s2)
 - ESP32-S2 
 - ESP32-S2F
 - ESP32-S2-MINI-2/2U 
 - ESP32-S2-SOLO-2/2U 
 - ESP32-S2-MINI-1/1U 
 - ESP32-S2-SOLO (-U)
 - ESP32-S2-DevKitM-1 
 - ESP32-S2-DevKitC-1 
 - ESP32-S2-Kaluga-1

### ESP32-E
[E22](https://www.espressif.com/en/products/socs/esp32-e22)
 - ESP32-E22
 - ESP32-E22-M2-1

### ESP32-P
[P4](https://www.espressif.com/en/products/socs/esp32-p4)
 - ESP32-P4
 - ESP32-P4-Function-EV-Board

## Программное обеспечение
| Решение                          | Сайт                                                                   | Докуметация                                                                | Код                                                     |
|----------------------------------|------------------------------------------------------------------------|:---------------------------------------------------------------------------|---------------------------------------------------------|
| ESP-IDF                          | [ESP-IDF](https://developer.espressif.com/tags/esp-idf/)               | [Docs](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/)       | [GitHub](https://github.com/espressif/esp-idf)          |
| Arduino                          | [Arduino for Espressif](https://www.espressif.com/en/sdks/esp-arduino) | [Docs](https://docs.espressif.com/projects/arduino-esp32/en/latest/)       | [GitHub](https://github.com/espressif/arduino-esp32)    |
| Zephyr                           | [Zephyr® RTOS](https://www.espressif.com/en/sdks/esp-zephyr)           | [Official documentation](https://docs.zephyrproject.org/latest/index.html) | [GitHub](https://github.com/zephyrproject-rtos/zephyr)  |
| Rust                             | [The Rust on ESP Book](https://developer.espressif.com/tags/zephyr/)   | [Docs](https://docs.espressif.com/projects/rust/book/)                     | [Awesome](https://github.com/esp-rs/awesome-esp-rust)   |

## Проекты

## Публикации

[Подключение плат ESP32 в Arduino](docs/ARDUINO.md)

## Прочти меня


## Глоссарий
