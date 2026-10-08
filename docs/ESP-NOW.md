# ESP-NOW

[← Назад к Roadmap](../README.md) · [↑ Наверх](#top)

<a name="top"></a>

## Обзор

ESP-NOW — протокол беспроводной связи от Espressif для микроконтроллеров. Обеспечивает связь **без точки доступа (AP)** с низким энергопотреблением, низкой задержкой и высокой пропускной способностью. Подходит для умных светильников, пультов, датчиков.

## Ключевые особенности

- **Без роутера** — прямая связь между устройствами.
- **Низкая задержка** — для задач реального времени.
- **Низкое энергопотребление** — для батарейных устройств.
- **Peer-to-Peer** — прямая связь между устройствами.
- **Масштабируемость** — несколько пиров в сети.
- **Шифрование** — встроенная поддержка.

## Поддерживаемые чипы

Протестирован на семействе **ESP32** (ESP32, ESP32-S3, ESP32-C3, ESP32-C6). Совместимость с ESP8266 или другими платформами не гарантируется. Чипы серии **ESP32-H** (H2, H4) не поддерживают ESP-NOW — у них нет Wi-Fi.

## Требования

- Любая плата ESP32 с Wi-Fi, поддерживаемая Arduino.
- Arduino IDE.
- Arduino Core for ESP32 **v3.0.0 или новее**.

## API (Arduino)

### ESP_NOW_Class

Основной класс для управления соединением:

- `begin(pmk)` — инициализация. `pmk` — опциональный pairwise master key при шифровании.
- `end()` — завершение, освобождение ресурсов.
- `getTotalPeerCount()` — количество пиров.
- `getEncryptedPeerCount()` — количество пиров с шифрованием.
- `onNewPeer(cb, arg)` — регистрация коллбэка для новых пиров.

### ESP_NOW_Peer

Класс пира. Абстрактный — должен наследоваться дочерним классом с реализацией `_onReceive` и `_onSent`.

- Конструктор: `ESP_NOW_Peer(mac_addr, channel, iface, lmk)`.
- `add()` — добавить пира.
- `remove()` — удалить пира.
- `send(data, len)` — отправить данные.
- `addr()` — получить MAC-адрес.
- `setKey(lmk)` — установить LMK.
- `onReceive(data, len, broadcast)` — коллбэк приёма.
- `onSent(success)` — коллбэк отправки.

## Пример: два устройства (ESP_NOW_Serial)

Для связи двух устройств используется `ESP_NOW_Serial_Class`:

```cpp
#include "ESP32_NOW_Serial.h"
#include "MacAddress.h"
#include "WiFi.h"
#include "esp_wifi.h"

#define ESPNOW_WIFI_CHANNEL 1
#define ESPNOW_WIFI_MODE WIFI_STA
#define ESPNOW_WIFI_IF WIFI_IF_STA

const MacAddress peer_mac({0xF4, 0x12, 0xFA, 0x40, 0x64, 0x4C});

ESP_NOW_Serial_Class NowSerial(peer_mac, ESPNOW_WIFI_CHANNEL, ESPNOW_WIFI_IF);

void setup() {
    Serial.begin(115200);
    WiFi.mode(ESPNOW_WIFI_MODE);
    WiFi.setChannel(ESPNOW_WIFI_CHANNEL, WIFI_SECOND_CHAN_NONE);
    while (!WiFi.STA.started()) delay(100);

    Serial.print("MAC: ");
    Serial.println(WiFi.macAddress());

    NowSerial.begin(115200);
}

void loop() {
    while (NowSerial.available()) Serial.write(NowSerial.read());
    while (Serial.available() && NowSerial.availableForWrite()) {
        if (NowSerial.write(Serial.read()) <= 0) {
            Serial.println("Failed to send");
            break;
        }
    }
    delay(1);
}
```

## Пример: broadcast

Broadcast-адрес `FF:FF:FF:FF:FF:FF` доступен как `ESP_NOW.BROADCAST_ADDR`:

```cpp
#include <Arduino.h>
#include "ESP32_NOW.h"
#include "WiFi.h"

#define ESPNOW_WIFI_CHANNEL 6

class ESP_NOW_Broadcast_Peer : public ESP_NOW_Peer {
public:
    ESP_NOW_Broadcast_Peer(uint8_t channel, wifi_interface_t iface, const uint8_t *lmk)
        : ESP_NOW_Peer(ESP_NOW.BROADCAST_ADDR, channel, iface, lmk) {}

    ~ESP_NOW_Broadcast_Peer() { remove(); }

    bool begin() {
        if (!ESP_NOW.begin() || !add()) {
            log_e("Failed to initialize");
            return false;
        }
        return true;
    }

    bool send_message(const uint8_t *data, size_t len) {
        if (!send(data, len)) {
            log_e("Failed to broadcast");
            return false;
        }
        return true;
    }
};

uint32_t msg_count = 0;
ESP_NOW_Broadcast_Peer broadcast_peer(ESPNOW_WIFI_CHANNEL, WIFI_IF_STA, nullptr);

void setup() {
    Serial.begin(115200);
    WiFi.mode(WIFI_STA);
    WiFi.setChannel(ESPNOW_WIFI_CHANNEL);
    while (!WiFi.STA.started()) delay(100);
    broadcast_peer.begin();
}

void loop() {
    char msg[32];
    sprintf(msg, "Message %lu", msg_count++);
    broadcast_peer.send_message((uint8_t*)msg, strlen(msg));
    delay(5000);
}
```

## Ограничения

- Максимальный размер пакета — 250 байт.
- Не поддерживается на ESP32-H (нет Wi-Fi).
- Требуется Arduino core v3.0.0 или новее.
- При использовании шифрования — максимум 17 пиров.

## Ресурсы

- [ESP-NOW — официальная страница](https://www.espressif.com/en/solutions/low-power-solutions/esp-now)
- [ESP-NOW API (Arduino)](https://docs.espressif.com/projects/arduino-esp32/en/latest/api/espnow.html)
- [Using ESP-NOW in Arduino](https://developer.espressif.com/blog/2024/08/arduino-esp-now-lib/)
- [ESP-NOW на GitHub](https://github.com/espressif/esp-now)

[← Назад к Roadmap](../README.md) · [↑ Наверх](#top)