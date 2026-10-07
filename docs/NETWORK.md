# Связь и сети на ESP32: ESP-NOW, LoRa и Meshtastic

> Отдельная страница Roadmap ESP32. Здесь собрано всё о беспроводной связи на ESP32 за пределами обычного Wi-Fi: протокол ESP-NOW, LoRa-модули, mesh-сеть Meshtastic, примеры кода и реальные проекты.

---

## Содержание

- [Зачем нужны альтернативные каналы связи](#зачем-нужны-альтернативные-каналы-связи)
- [ESP-NOW](#esp-now)
  - [Как это работает](#как-это-работает-esp-now)
  - [Пример кода: отправитель и получатель](#пример-кода-отправитель-и-получатель)
- [LoRa](#lora)
  - [Как это работает](#как-это-работает-lora)
  - [Модули и подключение](#модули-и-подключение)
  - [Пример кода: приём и передача пакетов](#пример-кода-приём-и-передача-пакетов)
- [Meshtastic](#meshtastic)
  - [Архитектура и протокол](#архитектура-и-протокол)
  - [Сборка прошивки](#сборка-прошивки)
- [Сравнение технологий](#сравнение-технологий)
- [Проекты](#проекты)
- [Полезные ссылки](#полезные-ссылки)

---

## Зачем нужны альтернативные каналы связи

Классический Wi-Fi на ESP32 хорош для подключения к интернету, но у него есть ограничения: высокое энергопотребление, необходимость в роутере и ограниченная дальность. Для задач, где нужна прямая связь между устройствами, дальняя передача на километры или работа от батарей месяцами, существуют другие протоколы.

**ESP-NOW** — это проприетарный протокол Espressif для прямой связи между устройствами без роутера. **LoRa** — это радиотехнология дальнего действия с низким энергопотреблением. **Meshtastic** — это открытая mesh-сеть, построенная поверх LoRa, позволяющая устройствам ретранслировать сообщения друг друга без интернета и инфраструктуры.

---

## ESP-NOW

ESP-NOW — это протокол беспроводной связи, разработанный Espressif для микроконтроллеров ESP32 и ESP8266. Он обеспечивает быструю, низкопотребляющую передачу данных между устройствами **напрямую, без Wi-Fi-роутера и интернета**.

### Как это работает (ESP-NOW)

- **Привязка по MAC-адресу:** устройства обмениваются данными напрямую, используя MAC-адреса друг друга для идентификации.
- **Малые пакеты, низкая задержка:** максимальный размер пакета — **250 байт**, задержка минимальна, что идеально для сенсорных сетей и систем удалённого управления.
- **Топологии:** поддерживаются **один-ко-многим** и **многие-к-одному**, что позволяет строить как централизованные, так и распределённые сети.
- **Работа без Wi-Fi:** протокол функционирует даже при отключённом или неподключённом Wi-Fi, что снижает энергопотребление.
- **Шифрование:** встроенная поддержка шифрования для безопасной передачи данных.

**Ограничения:** ESP-NOW не поддерживается на чипах серии **ESP32-H** (H2, H4), так как у них нет Wi-Fi. Для работы требуется Arduino core **v3.0.0 или новее**.

### Пример кода: отправитель и получатель

**Получатель** — сначала узнаём его MAC-адрес:

```cpp
#include <WiFi.h>

void setup() {
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  Serial.println("ESP32 MAC Address:");
  Serial.println(WiFi.macAddress());
}

void loop() {}
```

**Отправитель** — читает данные с датчика и отправляет их по ESP-NOW:

```cpp
#include <WiFi.h>
#include <esp_now.h>

#define MQ2_PIN 36  // Аналоговый вход датчика MQ-2

// MAC-адрес получателя (замените на свой)
uint8_t receiverAddress[] = { 0x14, 0x33, 0x5C, 0x02, 0xC0, 0xFC };

typedef struct struct_message {
  int gasValue;
} struct_message;

struct_message message;

// Callback при отправке
void OnSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
  Serial.print("Send status: ");
  Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Success" : "Fail");
}

void setup() {
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  Serial.println(WiFi.macAddress());

  if (esp_now_init() != ESP_OK) {
    Serial.println("ESP-NOW init failed");
    return;
  }

  esp_now_register_send_cb(OnSent);

  esp_now_peer_info_t peerInfo = {};
  memcpy(peerInfo.peer_addr, receiverAddress, 6);
  peerInfo.channel = 0;
  peerInfo.encrypt = false;

  if (esp_now_add_peer(&peerInfo) != ESP_OK) {
    Serial.println("Failed to add peer");
    return;
  }
}

void loop() {
  int gasReading = analogRead(MQ2_PIN);
  message.gasValue = gasReading;
  esp_now_send(receiverAddress, (uint8_t *)&message, sizeof(message));
  delay(1000);
}
```

Этот пример демонстрирует передачу данных с газового датчика MQ-2 между двумя ESP32. Для более сложных сценариев (несколько пиров, класс `ESP_NOW_Peer`) обратитесь к официальной документации.

---

## LoRa

LoRa (Long Range) — это технология модуляции радиосигнала, обеспечивающая **дальнюю связь (километры) при крайне низком энергопотреблении**. Она работает в нелицензируемых диапазонах (433, 868, 915 МГц) и идеальна для устройств, которые должны работать от батареи годами.

### Как это работает (LoRa)

- **Модуляция Chirp Spread Spectrum (CSS):** данные кодируются в виде «чирпов» — сигналов с изменяющейся частотой. Это делает сигнал устойчивым к шумам и помехам.
- **Высокая чувствительность приёмника:** до **-148 dBm**, что позволяет принимать очень слабые сигналы на больших расстояниях.
- **Компромисс:** скорость передачи данных низкая (от 0.3 до 50 кбит/с), что компенсируется дальностью и энергоэффективностью.
- **Модули:** на ESP32 чаще всего используются **SX1276/SX1278** (433/868/915 МГц) и **SX1262** (более современный). Подключение — по SPI.

### Модули и подключение

Для работы с LoRa на ESP32 обычно используются готовые платы или модули:

| Модуль | Чип | Частота | Интерфейс | Примечание |
|--------|-----|---------|-----------|-----------|
| **TTGO LoRa32** | SX1276 | 868/915 МГц | SPI | Популярная плата с OLED |
| **RFM95** | SX1276 | 868/915 МГц | SPI | Компактный модуль |
| **EBYTE E32** | SX1278 | 433 МГц | UART | Простая интеграция через AT-команды |
| **EBYTE E22** | SX1262 | 868/915 МГц | SPI | Высокая мощность, до 30 dBm |

Подключение по SPI требует всего четырёх сигналов: **SCK, MOSI, MISO, SS**.

### Пример кода: приём и передача пакетов

**Инициализация** (для платы TTGO LoRa32 V2.1 с SX1276):

```cpp
#include <RadioLib.h>

// Пины для TTGO LoRa32 V2.1
#define LORA_NSS   18
#define LORA_DIO0  26
#define LORA_RST   23
#define LORA_DIO1  33

SX1276 radio = new Module(LORA_NSS, LORA_DIO0, LORA_RST, LORA_DIO1);

void setup() {
  Serial.begin(115200);

  // Инициализация SX1276 на частоте 868 МГц
  int state = radio.begin(868.0);
  if (state != RADIOLIB_ERR_NONE) {
    Serial.println("LoRa init failed!");
    while (true);
  }

  Serial.println("LoRa init OK");
}

void loop() {
  // Отправка пакета
  String message = "Hello from ESP32!";
  int state = radio.transmit(message);
  if (state == RADIOLIB_ERR_NONE) {
    Serial.println("Send success");
  }

  delay(5000);

  // Приём пакета
  String received;
  state = radio.receive(received);
  if (state == RADIOLIB_ERR_NONE) {
    Serial.print("Received: ");
    Serial.println(received);
  }
}
```

Библиотека **RadioLib** поддерживает SX1276, SX1262 и другие популярные LoRa-чипы. Для модулей EBYTE E32/E22 есть специализированные библиотеки с более простым API.

---

## Meshtastic

Meshtastic — это **открытая децентрализованная mesh-сеть на базе LoRa**. Каждое устройство (нода) не только отправляет свои сообщения, но и **ретранслирует сообщения соседей**, расширяя покрытие сети. Интернет и инфраструктура не требуются.

### Архитектура и протокол

**Типичная нода** — это плата на ESP32 или nRF52840 с LoRa-трансивером и, опционально, GPS-приёмником.

**Как работает mesh:**

- Сообщение отправляется в эфир.
- Все ноды в радиусе слышат его.
- Ноды, которые ещё не видели это сообщение, **ретранслируют его дальше**, с ограничением по количеству хопов и подавлением дублирующих ретрансляций, чтобы «флуд» не вышел из-под контроля.
- Сообщения шифруются с использованием **AES-CTR**.

**Топология:** децентрализованная. Нет «главной» ноды; сеть продолжает работать, даже если отдельные устройства выходят из строя.

**Интеграция с ESP-IDF:** существует компонент **`espp/meshtastic`** — минимальная реализация Meshtastic-совместимого mesh-узла. Он реализует фрейминг, AES-CTR шифрование, отправку/приём текстовых сообщений, информации о нодах и позициях, дедупликацию и опциональную ретрансляцию. Компонент **не привязан к конкретному радио** — вы предоставляете функцию передачи и подаёте принятые кадры. В примере используется совместно с компонентом `espp/sx126x` (SX1262).

**Ограничения компонента:** не реализованы PKI-шифрованные прямые сообщения, MQTT, телеметрия, store-and-forward, админ-протокол и SNR-взвешенное время ретрансляции.

### Сборка прошивки

Meshtastic использует **PlatformIO** для сборки.

```bash
# 1. Клонирование репозитория
git clone https://github.com/meshtastic/firmware.git
cd firmware

# 2. Обновление подмодулей
git submodule update --init

# 3. Открыть папку в VS Code с установленным PlatformIO
# 4. Выбрать целевое устройство через Command Palette:
#    PlatformIO: Pick Project Environment
# 5. Собрать: PlatformIO: Build
# 6. Прошить: PlatformIO: Upload
```

Полная инструкция — в официальной документации.

---

## Сравнение технологий

| Характеристика | **ESP-NOW** | **LoRa** | **Meshtastic** |
|---------------|------------|----------|----------------|
| **Дальность** | До 200–500 м (в помещении) | До 5–15 км (на открытой местности) | Зависит от количества нод; каждый хоп расширяет покрытие |
| **Скорость** | Высокая (Мбит/с) | Низкая (0.3–50 кбит/с) | Низкая (как у LoRa) |
| **Энергопотребление** | Среднее | Очень низкое | Очень низкое |
| **Топология** | Звезда / точка-точка | Точка-точка | Mesh (децентрализованная) |
| **Нужен роутер** | Нет | Нет | Нет |
| **Шифрование** | Встроенное | Опционально | AES-CTR (встроенное) |
| **Стоимость** | Встроено в ESP32 | Модуль SX1276: ~$3–5 | Плата: ~$15–30 |
| **Типовое применение** | Сенсорные сети в доме, пульты | Датчики на улице, счётчики | Оффлайн-связь, походы, ЧС |

**Комбинированные системы:**

- **ESP-NOW → LoRa → Meshtastic:** ноды на ESP-NOW собирают данные с локальных датчиков и через центральную ESP32 отправляют их в LoRa-сеть Meshtastic.
- **Meshtastic + ESP-NOW bridge:** захват входящих сообщений через ESP-NOW и пересылка их на Meshtastic-устройство через UART.

---

## Проекты

### ESP-NOW

| Проект | Описание | Ссылка |
|--------|----------|--------|
| **Farm-Data-Relay-System** | Система для фермы: сбор данных с датчиков через ESP-NOW и LoRa | [GitHub](https://github.com/timmbogner/Farm-Data-Relay-System) |
| **ESP-NOW WLED Controller** | Удалённое управление WLED-лентами по ESP-NOW | [GitHub](https://github.com/AlLaguna/ESP-NOW-WLED-Controller) |
| **ESP-NOW Weather Station** | Беспроводная погодная станция на BME280 без Wi-Fi | [GitHub](https://github.com/educ8s/ESP32-ESPNOW-Examples) |
| **Elevator-ESP32** | Мини-лифт с автономным управлением на ESP-NOW и машиной состояний | [GitHub](https://github.com/delonet-ai/Elevator-ESP32) |
| **ESP-NOW Relay & Sensor System** | Двунаправленная связь с реле и DHT-датчиком (MicroPython) | [GitHub](https://github.com/kritishmohapatra/100_Days_100_IoT_Projects) |

### LoRa

| Проект | Описание | Ссылка |
|--------|----------|--------|
| **LoRa APRS iGate** | LoRa APRS iGate/Digi на ESP32, приём и передача APRS | [GitHub](https://github.com/richonguzman/LoRa_APRS_iGate) |
| **lopfara** | Мониторинг растений на TTGO LoRa32 с SX1276 | [GitHub](https://github.com/mcbernie/lopfara) |
| **Talk-E** | Простая LoRa-рация на ESP32-S3, 433 МГц | [GitHub](https://github.com/milovanpms/talk-e) |
| **ESP32_S3 LoRa Board** | Плата ESP32-S3 + SX1278 для Meshtastic и ExpressLRS | [GitHub](https://github.com/Ale-maker325/ESP32_S3_33_433MHz_SX1278) |
| **E220 Deep Sleep** | LoRa E22 + ESP32 с FreeRTOS, глубокий сон и пробуждение по радио | [GitHub](https://github.com/Tech500/E220-WOR-with-Dual-Core-FREERTOS-System) |

### Meshtastic

| Проект | Описание | Ссылка |
|--------|----------|--------|
| **Meshtastic Firmware** | Официальная прошивка Meshtastic | [GitHub](https://github.com/meshtastic/firmware) |
| **espp/meshtastic** | Компонент Meshtastic для ESP-IDF (SX1262) | [ESP Component Registry](https://components.espressif.com/components/espp/meshtastic) |
| **ESP-NOW to Meshtastic Bridge** | Мост между ESP-NOW и Meshtastic через UART | [GitHub Discussion](https://github.com/meshtastic/firmware/discussions/6393) |
| **WildCAM_ESP32** | Реализация Meshtastic-протокола для камеры наблюдения | [GitHub](https://github.com/thewriterben/WildCAM_ESP32) |

---

## Полезные ссылки

### Официальная документация

- [ESP-NOW — Espressif](https://www.espressif.com/en/solutions/low-power-solutions/esp-now) — официальная страница протокола
- [ESP-NOW Arduino Library Documentation](https://docs.espressif.com/projects/arduino-esp32/en/latest/api/espnow.html) — API новой библиотеки
- [Using ESP-NOW in Arduino](https://developer.espressif.com/blog/2024/08/arduino-esp-now-lib/) — официальный блог с примерами
- [Meshtastic Documentation](https://meshtastic.org/docs/) — полная документация проекта
- [Building Meshtastic Firmware](https://meshtastic.org/docs/development/firmware/build/) — инструкция по сборке

### Библиотеки и компоненты

- [RadioLib](https://github.com/jgromes/RadioLib) — универсальная библиотека для LoRa-чипов (SX1276, SX1262 и др.)
- [LoRa E32 Series Library](https://github.com/xreef/LoRa_E32_Series_Library) — библиотека для EBYTE E32
- [espp/meshtastic](https://components.espressif.com/components/espp/meshtastic) — Meshtastic-компонент для ESP-IDF
- [QuickESPNow](https://github.com/gmag11/QuickESPNow) — упрощённая библиотека ESP-NOW

### Обучающие материалы

- [Getting Started with ESP-NOW](https://www.cytron.io/tutorial/wireless-iot/getting-started-espnow) — пошаговый туториал с газовым датчиком
- [ESP-NOW in MicroPython](https://docs.micropython.org/en/latest/library/espnow.html) — документация MicroPython
- [Meshtastic Development Tutorial](https://wiki.seeedstudio.com/meshtastic_firmware_source_code_tutorial/) — сборка прошивки на Seeed Studio

### Покупка

- **TTGO LoRa32 V2.1** — ESP32 + SX1276 + OLED, ~$15–20
- **Heltec WiFi LoRa 32** — ESP32-S3 + SX1262 + OLED, ~$20–25
- **LILYGO T-Beam** — ESP32 + SX1262 + GPS, для Meshtastic, ~$30–40
- **EBYTE E32/E22** — модули LoRa с UART/SPI, ~$3–8

---

*Последнее обновление: октябрь 2026. Если найдёте неточность или хотите добавить проект — правьте смело.*