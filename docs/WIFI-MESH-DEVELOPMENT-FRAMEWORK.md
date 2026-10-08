# Wi-Fi Mesh development framework

[← Назад к Roadmap](../README.md) · [↑ Наверх](#top)

<a name="top"></a>

## Обзор

ESP-WIFI-MESH — самоорганизующаяся mesh-сеть на базе Wi-Fi от Espressif для чипов ESP32. Позволяет строить сети из сотен устройств, где каждый узел ретранслирует данные, расширяя покрытие. Сеть автоматически восстанавливается при выходе узлов из строя.

## Архитектура

### Стек ПО

ESP-WIFI-MESH построен поверх Wi-Fi драйвера и FreeRTOS. В некоторых случаях (корневой узел) используется стек LwIP.

### Системные события

Приложение взаимодействует с ESP-WIFI-MESH через **ESP-WIFI-MESH Events**. `mesh_event_id_t` определяет все возможные события, включая подключение/отключение родителя/потомка.

Перед использованием событий приложение должно зарегистрировать обработчик через `esp_event_handler_register()`.

Типичные события:

- `MESH_EVENT_PARENT_CONNECTED` — узел может начать передачу данных вверх по сети.
- `MESH_EVENT_CHILD_CONNECTED` — узел может начать передачу данных вниз по сети.
- `IP_EVENT_STA_GOT_IP` — корневой узел может передавать данные во внешнюю IP-сеть.

**Предупреждение:** в self-organized режиме нельзя вызывать Wi-Fi API после `esp_mesh_start()` и до `esp_mesh_stop()`.

### LwIP и ESP-WIFI-MESH

Приложение может обращаться к стеку ESP-WIFI-MESH напрямую, без LwIP. LwIP нужен только корневому узлу для связи с внешней IP-сетью. Однако каждый узел должен инициализировать LwIP (через `esp_netif_init()`), так как может стать корневым.

### Топология

ESP-WIFI-MESH использует древовидную топологию. Максимальное количество слоёв — **25** для древовидной топологии, **1000** для цепной.

## Программирование

### Предварительные требования

Перед запуском ESP-WIFI-MESH необходимо инициализировать LwIP и Wi-Fi:

```c
ESP_ERROR_CHECK(esp_netif_init());
ESP_ERROR_CHECK(esp_event_loop_create_default());

wifi_init_config_t config = WIFI_INIT_CONFIG_DEFAULT();
ESP_ERROR_CHECK(esp_wifi_init(&config));

ESP_ERROR_CHECK(esp_event_handler_register(IP_EVENT, IP_EVENT_STA_GOT_IP,
                                           &ip_event_handler, NULL));
ESP_ERROR_CHECK(esp_wifi_set_storage(WIFI_STORAGE_FLASH));
ESP_ERROR_CHECK(esp_wifi_start());
```

### Три шага запуска

**1. Инициализация Mesh:**

```c
ESP_ERROR_CHECK(esp_mesh_init());
ESP_ERROR_CHECK(esp_event_handler_register(MESH_EVENT, ESP_EVENT_ANY_ID,
                                           &mesh_event_handler, NULL));
```

**2. Настройка сети:**

Выполняется через `esp_mesh_set_config()` со структурой `mesh_cfg_t`.

**3. Запуск:**

```c
esp_mesh_start();
```

### Режимы работы

- **Router Mode** — корневой узел подключён к Wi-Fi роутеру.
- **No Router Mode** — сеть работает автономно.

### Максимальное количество слоёв

Устанавливается через `esp_mesh_set_max_layer()`.

## ESP-MDF (фреймворк)

Для упрощения разработки существует **ESP-MDF** (Espressif Mesh Development Framework).

### Возможности

- **Быстрая настройка сети** — устройства автономно и быстро устанавливают соединение.
- **Стабильное обновление** — автоматическая повторная передача сбойных фрагментов, сжатие данных, откат к предыдущей версии, проверка прошивки.
- **Эффективная отладка** — беспроводная передача логов, беспроводная отладка, отладочный терминал.
- **LAN-управление** — сеть может управляться приложением, сенсором и т.д.
- **Демо-приложения** — решения для освещения, сенсоров, кнопок.

### Структура ESP-MDF

Состоит из Utils, Components и Examples.

**Utils:**

- **Third Party** — сторонние библиотеки.
- **Driver** — драйверы для кнопок, LED.
- **Miniz** — библиотека сжатия.
- **Aliyun** — Aliyun IoT kit.
- **Mwifi** — добавляет к ESP-WIFI-MESH фильтр повторной передачи, сжатие данных, фрагментированную передачу, P2P multicast.
- **Mespnow** — добавляет к ESP-NOW фильтр повторной передачи, CRC, фрагментацию.

**Components:**

- **Mconfig** — модуль настройки сети.
- **Mupgrade** — модуль обновления.
- **Mdebug** — модуль отладки.
- **Mlink** — модуль LAN-управления.

### Установка ESP-MDF

```bash
cd ~/esp
git clone --recursive https://github.com/espressif/esp-mdf.git
```

Установите переменную окружения `MDF_PATH`.

### Сборка

```bash
cd ~/esp/get-started
make menuconfig
make erase_flash flash -j5 monitor
```

## Ресурсы

- [ESP-WIFI-MESH Programming Guide](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/network/esp-wifi-mesh.html)
- [ESP-WIFI-MESH API Guide](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/esp-wifi-mesh.html)
- [ESP-MDF на GitHub](https://github.com/espressif/esp-mdf)
- [ESP-MDF Documentation](https://docs.espressif.com/projects/esp-mdf/en/latest/)
- [Wi-Fi Mesh — официальная страница](https://www.espressif.com/en/products/sdks/esp-wifi-mesh/overview)

[← Назад к Roadmap](../README.md) · [↑ Наверх](#top)