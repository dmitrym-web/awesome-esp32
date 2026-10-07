# Roadmap ESP32 (актуализировано)

[ESP32](https://ru.wikipedia.org/wiki/ESP32) — серия микроконтроллеров с малым энергопотреблением китайской компании [Espressif Systems](https://www.espressif.com/). ESP32 создан и разработан компанией, расположенной в Шанхае, а производится компанией TSMC по техпроцессу 40 нм и 28 нм. Серия является преемником микросхем ESP8266.

## Содержание

- [Решения](#решения)
- [Аппаратное обеспечение](#аппаратное-обеспечение)
  - [ESP8266](#esp8266)
  - [Классический ESP32](#классический-esp32)
  - [ESP32-S](#esp32-s)
  - [ESP32-C](#esp32-c)
  - [ESP32-H](#esp32-h)
  - [ESP32-P](#esp32-p)
  - [ESP32-E](#esp32-e)
- [Сравнение архитектур и областей применения](#сравнение-архитектур-и-областей-применения)
- [Программное обеспечение](#программное-обеспечение)
- [Проекты](#проекты)
- [Публикации](#публикации)
- [Глоссарий](#глоссарий)

---

## Решения

| Решение | Сайт | Документация | Код |
|---------|------|-------------|-----|
| AI (ESP-DL, ESP-NN, ESP-SR, ESP-WHO) | [Espressif AI](https://www.espressif.com/en/products/sdks/esp-ai) | [Docs](https://docs.espressif.com/projects/esp-dl/en/latest/) | [GitHub](https://github.com/espressif/esp-dl) |
| AT application | [ESP-AT](https://www.espressif.com/en/products/sdks/esp-at) | [Docs](https://docs.espressif.com/projects/esp-at/en/latest/) | [GitHub](https://github.com/espressif/esp-at) |
| Audio development framework | [ESP-ADF](https://www.espressif.com/en/products/sdks/esp-adf) | [Docs](https://docs.espressif.com/projects/esp-adf/en/latest/) | [GitHub](https://github.com/espressif/esp-adf) |
| BLE Mesh development framework | [BLE Mesh](https://www.espressif.com/en/products/sdks/esp-idf/esp-ble-mesh) | [Docs](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-guides/esp-ble-mesh/ble-mesh-index.html) | |
| Camera application | [ESP-WHO](https://github.com/espressif/esp-who) | | [GitHub](https://github.com/espressif/esp-who) |
| ESP Matter | [ESP Matter](https://www.espressif.com/en/solutions/device-connectivity/esp-matter-solution) | [Docs](https://docs.espressif.com/projects/esp-matter/en/latest/esp32/) | [GitHub](https://github.com/espressif/esp-matter) |
| ESP-NOW | [ESP-NOW](https://www.espressif.com/en/solutions/low-power-solutions/esp-now) | [Docs](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/api-reference/network/esp_now.html) | [GitHub](https://github.com/espressif/esp-now) |
| ESP RainMaker cloud service | [ESP RainMaker](https://rainmaker.espressif.com/) | [Docs](https://docs.rainmaker.espressif.com/) | [GitHub](https://github.com/espressif/esp-rainmaker) |
| Wi-Fi Mesh development framework | [Wi-Fi Mesh](https://www.espressif.com/en/products/sdks/esp-wifi-mesh/overview) | [Docs](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/esp-wifi-mesh.html) | [GitHub](https://github.com/espressif/esp-mesh-lite) |

### ESP Matter

[Matter](https://ru.wikipedia.org/wiki/Matter_(%D1%81%D1%82%D0%B0%D0%BD%D0%B4%D0%B0%D1%80%D1%82)) — единый стандарт подключения для умного дома с открытым исходным кодом, позволяющий объединять IoT-устройства разных производителей в единую сеть. Стандарт работает на основе интернет-протокола (IP) и функционирует через один или несколько контроллеров (хабов).

**Роли ESP32 в экосистеме Matter:**

- **Wi-Fi End Device** — модули ESP32, ESP32-C и ESP32-S с поддержкой Wi-Fi.
- **Thread End Device** — ESP32-H (H2, H4, H21) и модули с поддержкой IEEE 802.15.4.
- **Ethernet End Device** — ESP32 и ESP32-S с внешним контроллером Ethernet.
- **Thread Border Router** — ESP32-H + ESP32-S3 или другие микроконтроллеры для подключения сетей Thread к Wi-Fi.
- **Matter Bridge** — ESP32-H + ESP32-S3 для создания моста Matter-Zigbee.

**Ресурсы:**

- [CSA](https://csa-iot.org/ru/)
- [CSA Matter](https://csa-iot.org/ru/all-solutions/matter/)
- [Open-Source Matter SDK](https://github.com/project-chip/connectedhomeip)
- [Espressif's SDK for Matter](https://github.com/espressif/esp-matter)
- [Документация Matter](https://developer.espressif.com/blog/matter/)
- [Home Assistant интеграция](https://www.home-assistant.io/integrations/matter/)

Модули ESP-ZeroCode основаны на ESP32-C3 (ESP8685), ESP32-C2 (ESP8684) и ESP32-H2.

---

## Аппаратное обеспечение

[DOCS](https://www.espressif.com/en/support/documents/technical-documents)

### ESP8266

> *Исправление: в исходном файле было «ESP8286» — это опечатка.*

- ESP8266EX
- ESP8285 (версия со встроенной flash-памятью)
- ESP-WROOM-02D/02U
- ESP-WROOM-02
- ESP8266-DevKitC
- ESP-Launcher

**Архитектура:** Tensilica L106, 32-бит, одно ядро, до 160 МГц. Wi-Fi 802.11 b/g/n. Предшественник ESP32, до сих пор используется в простых IoT-устройствах.

### Классический ESP32

- ESP32-D0WD-V3
- ESP32-D0WDR2-V3
- ESP32-U4WDH
- ESP32-PICO-V3 / V3-02 / D4
- ESP32-WROOM-32E/32UE
- ESP32-WROOM-DA
- ESP32-WROVER-E/IE
- ESP32-MINI-1/1U
- ESP32-PICO-MINI-02/02U
- ESP32-PICO-V3-ZERO (ACK Module)
- ESP32-DevKitC / DevKitM-1
- ESP32-PICO-KIT
- ESP32-Vaquita-DSPG
- ESP32-LCDKit

**Архитектура:** Xtensa LX6, 32-бит, два ядра (PRO_CPU и APP_CPU), до 240 МГц. 520 КБ SRAM, 448 КБ ROM. Wi-Fi 802.11 b/g/n, Bluetooth 4.2 BR/EDR + BLE. Гарвардская архитектура с раздельными кэшами инструкций и данных. ULP-сопроцессор для работы в глубоком сне.

### ESP32-S

#### ESP32-S31

- ESP32-S31
- ESP32-S31-WROOM-1 / WROOM-3
- ESP32-S31-Korvo-1
- ESP32-S31-Function-Coreboard-1

**Архитектура:** двухъядерный RISC-V, до 320 МГц, один core с 128-битным трактом данных и SIMD-инструкциями. 512 КБ SRAM, поддержка 250 МГц 8-bit DDR PSRAM. Wi-Fi 6 (2.4 ГГц), Bluetooth 5.4 (LE + Classic), IEEE 802.15.4 (Thread/Zigbee), Gigabit Ethernet MAC, USB OTG High Speed. Аппаратные ускорители JPEG, 2D-графики (PPA), 2D-DMA. DVP-камера (8–16 бит), LCD (8–24 бит), до 14 каналов ёмкостного сенсора. PUF, TEE, защита от side-channel-атак.

**Применение:** AIoT-хабы, умные колонки, устройства с HMI, промышленная автоматизация, edge AI.

#### ESP32-S3

- ESP32-S3
- ESP32-S3-PICO-1
- ESP32-S3-WROOM-1/1U / WROOM-2
- ESP32-S3-MINI-1/1U
- ESP32-S3-DevKitC-1 / DevKitM-1
- ESP32-S3-BOX-3
- ESP32-S3-EYE
- ESP32-S3-Korvo-1 / Korvo-2
- ESP32-S3-LCD-EV-Board
- ESP32-S3-USB-OTG
- ESP32-S3-USB-Bridge

**Архитектура:** Xtensa LX7, 32-бит, два ядра, до 240 МГц. 512 КБ SRAM. Wi-Fi 4, Bluetooth 5.0 LE. **Векторные инструкции (128-bit SIMD / PIE)** для ускорения нейросетевых вычислений и цифровой обработки сигналов. USB-OTG Full Speed (12 Мбит/с). Поддержка LCD/Camera интерфейсов, до 8 МБ PSRAM.

**Применение:** edge AI, распознавание речи и изображений, HMI с дисплеем, USB-периферия.

#### ESP32-S2

- ESP32-S2
- ESP32-S2F
- ESP32-S2-MINI-1/1U / MINI-2/2U
- ESP32-S2-SOLO (-U) / SOLO-2/2U
- ESP32-S2-DevKitM-1 / DevKitC-1
- ESP32-S2-Kaluga-1

**Архитектура:** Xtensa LX7, одно ядро, до 240 МГц. Wi-Fi 4 (только 2.4 ГГц), **без Bluetooth**. USB-OTG Full Speed. 320 КБ SRAM. Подходит для задач, где нужен Wi-Fi и USB, но не нужен Bluetooth.

### ESP32-C

#### ESP32-C61

- ESP32-C61
- ESP32-C61-WROOM-1
- ESP32-C61-DevKitC-1

**Архитектура:** RISC-V, одно ядро, до 160 МГц. 320 КБ SRAM, 256 КБ ROM, поддержка внешней PSRAM. Wi-Fi 6 (2.4 ГГц) с OFDMA, MU-MIMO, TWT; Bluetooth 5 LE. Позиционируется как **экономичное решение с Wi-Fi 6** для массового IoT.

#### ESP32-C6

- ESP32-C6
- ESP32-C6-WROOM-1 / WROOM-1U
- ESP32-C6-MINI-1/1U
- ESP32-C6-DevKitC-1 / DevKitM-1

**Архитектура:** RISC-V, одно ядро HP (до 160 МГц) + LP-ядро. 512 КБ SRAM. Wi-Fi 6 (2.4 ГГц), Bluetooth 5 LE, **IEEE 802.15.4 (Thread 1.3, Zigbee 3.0)**. Ключевое семейство для Matter over Thread.

#### ESP32-C5

- ESP32-C5
- ESP32-C5-WROOM-1
- ESP32-C5-DevKitC-1

**Архитектура:** RISC-V, одно ядро, до 240 МГц. 384 КБ SRAM, 320 КБ ROM, внешняя PSRAM. **Первый RISC-V SoC с двухдиапазонным Wi-Fi 6 (2.4 + 5 ГГц)**, Bluetooth 5 LE, IEEE 802.15.4 (Zigbee/Thread). До 29 GPIO, SDIO, QSPI. LP-CPU до 40 МГц.

**Применение:** устройства, требующие 5 ГГц (снижение помех), Matter over Thread, высокоэффективная беспроводная передача.

#### ESP32-C3

- ESP32-C3
- ESP8685
- ESP32-C3-MINI-1/1U
- ESP32-C3-WROOM-02/02U
- ESP8685-WROOM-01 / 03 / 04 / 05 / 06 / 07
- ESP32-C3-DevKitM-1 / DevKitC-02
- ESP32-C3-DevKit-RUST-1
- ESP32-C3-AWS-ExpressLink-DevKit
- ESP32-C3-LCDkit
- ESP32-C3-Lyra

**Архитектура:** RISC-V, одно ядро, до 160 МГц. 400 КБ SRAM. Wi-Fi 4, Bluetooth 5 LE. Экономичная замена классическому ESP32 для простых IoT-задач.

#### ESP32-C2 (ESP8684)

- ESP8684
- ESP8684-MINI-1/1U
- ESP8684-WROOM-01C / 02C/02UC / 03 / 04C / 05 / 06C / 07
- ESP8684-DevKitM-1

**Архитектура:** RISC-V, одно ядро, до 120 МГц. ~272 КБ SRAM. Wi-Fi 4, Bluetooth 5 LE. **Самое дешёвое решение** для массовых IoT-устройств с минимальными требованиями.

### ESP32-H

#### ESP32-H4

- ESP32-H4
- ESP32-H4-WROOM-1

**Архитектура:** двухъядерный RISC-V, до 96 МГц (с DSP-расширениями). 384 КБ SRAM, 128 КБ ROM, поддержка внешней PSRAM. Bluetooth 5.4 LE (LE Audio, PAwR), IEEE 802.15.4. Позиционируется как **ультранизкопотребляющее решение** для батарейных устройств и HMI.

#### ESP32-H21

- ESP32-H21

**Архитектура:** ультранизкопотребляющий SoC с BLE 5 и 802.15.4.

#### ESP32-H2

- ESP32-H2
- ESP32-H2-MINI-1 / MINI-1U
- ESP32-H2-WROOM-02C
- ESP32-H2-DevKitM-1
- ESP Thread Border Router / Zigbee Gateway

**Архитектура:** RISC-V, одно ядро, до 96 МГц. 320 КБ SRAM (16 КБ кэш), 128 КБ ROM, 4 КБ LP Memory. Bluetooth 5.3 LE, IEEE 802.15.4 (Thread, Zigbee). 19 GPIO. **Без Wi-Fi** — специализированное решение для Thread/Zigbee и Matter over Thread.

### ESP32-P

#### ESP32-P4

- ESP32-P4
- ESP32-P4-Function-EV-Board

**Архитектура:** двухъядерный RISC-V, до **400 МГц**, с AI-расширениями и аппаратным FPU. 768 КБ HP L2MEM + 32 КБ LP SRAM. Поддержка до **32 МБ PSRAM**. **MIPI-CSI** (с ISP), **MIPI-DSI**, **H.264 encoder**, USB 2.0. Поддержка до 1080p видео. **Wi-Fi и Bluetooth отсутствуют** — требуется сопряжение с другим SoC (например, ESP32-C6) для беспроводной связи.

**Применение:** мультимедийные HMI, видеонаблюдение, промышленные панели управления, edge-вычисления с камерой и дисплеем.

### ESP32-E

#### ESP32-E22

- ESP32-E22
- ESP32-E22-M2-1

**Архитектура:** **Radio Co-Processor (RCP)** — не самостоятельный MCU, а сопроцессор для беспроводной связи. Двухъядерный RISC-V, до 500 МГц. 1 МБ on-chip памяти. **Трёхдиапазонный Wi-Fi 6E (2.4 + 5 + 6 ГГц)**, 160 МГц канал, 2×2 MU-MIMO, Beamforming, 1024-QAM, до **2.4 Гбит/с**. Bluetooth Classic (BR/EDR) + BLE 5.4. Интерфейсы к хосту: **PCIe 2.1, SDIO 3.0**.

**Применение:** высокопроизводительные беспроводные системы, AR/VR, промышленные шлюзы, потоковое видео.

---

## Сравнение архитектур и областей применения

| Семейство | Архитектура | Ядра | Макс. частота | Wi-Fi | Bluetooth | 802.15.4 | Ключевая особенность | Область применения |
|-----------|-------------|------|---------------|-------|-----------|----------|---------------------|-------------------|
| ESP8266 | Tensilica L106 | 1 | 160 МГц | Wi-Fi 4 | — | — | Дёшево, просто | Простой IoT |
| **ESP32** | Xtensa LX6 | 2 | 240 МГц | Wi-Fi 4 | BT 4.2 | — | Универсальность | Общего назначения |
| **ESP32-S2** | Xtensa LX7 | 1 | 240 МГц | Wi-Fi 4 | — | — | USB-OTG | USB-периферия |
| **ESP32-S3** | Xtensa LX7 | 2 | 240 МГц | Wi-Fi 4 | BT 5.0 | — | Векторные инструкции (AI) | Edge AI, HMI |
| **ESP32-S31** | RISC-V | 2 (+LP) | 320 МГц | Wi-Fi 6 | BT 5.4 + Classic | Thread/Zigbee | SIMD, Gigabit Ethernet, HMI | AIoT-хабы, колонки |
| **ESP32-C2** | RISC-V | 1 | 120 МГц | Wi-Fi 4 | BT 5.0 | — | Минимальная цена | Массовый IoT |
| **ESP32-C3** | RISC-V | 1 | 160 МГц | Wi-Fi 4 | BT 5.0 | — | Замена ESP32 | Бюджетный IoT |
| **ESP32-C5** | RISC-V | 1 (+LP) | 240 МГц | Wi-Fi 6 (2.4+5 ГГц) | BT 5.0 | Thread/Zigbee | 5 ГГц, Matter | Умный дом, Thread |
| **ESP32-C6** | RISC-V | 1 (+LP) | 160 МГц | Wi-Fi 6 | BT 5.0 | Thread/Zigbee | Matter over Thread | Умный дом |
| **ESP32-C61** | RISC-V | 1 | 160 МГц | Wi-Fi 6 | BT 5.0 | — | Экономичный Wi-Fi 6 | Массовый IoT |
| **ESP32-H2** | RISC-V | 1 | 96 МГц | — | BT 5.3 | Thread/Zigbee | Ультранизкое потребление | Батарейные сенсоры |
| **ESP32-H4** | RISC-V | 2 | 96 МГц | — | BT 5.4 | Thread/Zigbee | LE Audio, HMI | Батарейные устройства |
| **ESP32-H21** | RISC-V | 1 | — | — | BT 5 | Thread/Zigbee | Ультранизкое потребление | Сенсоры, метки |
| **ESP32-P4** | RISC-V | 2 | 400 МГц | — | — | — | MIPI-CSI/DSI, H.264 | Мультимедиа, HMI |
| **ESP32-E22** | RISC-V | 2 | 500 МГц | Wi-Fi 6E (2.4/5/6 ГГц) | BT 5.4 + Classic | — | RCP, 2.4 Гбит/с | AR/VR, шлюзы |

**Комбинированные системы:**

- **ESP32-P4 + ESP32-C6** — мультимедиа-устройство с Wi-Fi 6 и Thread/Zigbee.
- **ESP32-S3 + ESP32-H2** — edge AI + Thread Border Router.
- **ESP32-E22 + любой хост (Linux, ESP32-S3)** — высокоскоростная беспроводная связь.

---

## Программное обеспечение

| Решение | Сайт | Документация | Код |
|---------|------|-------------|-----|
| **ESP-IDF** | [ESP-IDF](https://developer.espressif.com/tags/esp-idf/) | [Docs](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/) | [GitHub](https://github.com/espressif/esp-idf) |
| **Arduino** | [Arduino for Espressif](https://www.espressif.com/en/sdks/esp-arduino) | [Docs](https://docs.espressif.com/projects/arduino-esp32/en/latest/) | [GitHub](https://github.com/espressif/arduino-esp32) |
| **Zephyr** | [Zephyr® RTOS](https://www.espressif.com/en/sdks/esp-zephyr) | [Docs](https://docs.zephyrproject.org/latest/index.html) | [GitHub](https://github.com/zephyrproject-rtos/zephyr) |
| **Rust** | [The Rust on ESP Book](https://docs.espressif.com/projects/rust/book/) | [Docs](https://docs.espressif.com/projects/rust/book/) | [Awesome ESP Rust](https://github.com/esp-rs/awesome-esp-rust) |

**Актуальные версии:**

- **ESP-IDF v6.0** — мажорное обновление, поддержка ESP32-C5, ESP32-C61, превью для H21 и H4.
- **Arduino core v3.x** — поддержка ESP32, S2, S3, C3, C6, H2, P4; C2 и C61 требуют сборки как компонента ESP-IDF.
- **Rust esp-hal v1.1** — стабильный no_std HAL для всех актуальных чипов.
- **Zephyr** — официальная поддержка Espressif, включая AMP на двухъядерных чипах.

---

## Проекты

*(раздел для заполнения — примеры open-source проектов на ESP32)*

## Публикации

- [Подключение плат ESP32 в Arduino](docs/ARDUINO.md)

## Глоссарий

- **RCP** — Radio Co-Processor, беспроводной сопроцессор.
- **PIE** — Processor Instruction Extensions, векторные расширения Xtensa для AI.
- **TEE** — Trusted Execution Environment, доверенная среда выполнения.
- **PUF** — Physically Unclonable Function, физически неклонируемая функция.
- **LP-CPU** — Low-Power CPU, энергоэффективное ядро.
- **ULP** — Ultra Low Power, сверхнизкопотребляющий сопроцессор.
- **TWT** — Target Wake Time, механизм энергосбережения Wi-Fi 6.
- **OFDMA** — Orthogonal Frequency-Division Multiple Access.
- **MU-MIMO** — Multi-User MIMO.
- **DVP** — Digital Video Port, интерфейс камеры.
- **ISP** — Image Signal Processor.
- **PPA** — Pixel Processing Accelerator.