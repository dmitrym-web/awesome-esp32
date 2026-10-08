# Camera application (ESP-WHO)

[← Назад к Roadmap](../README.md) · [↑ Наверх](#top)

<a name="top"></a>

## Обзор

ESP-WHO — платформа разработки для обработки изображений на чипах Espressif. Предоставляет фреймворк для компьютерного зрения: детекция лиц, распознавание лиц, детекция пешеходов, распознавание QR-кодов. Обработка выполняется на самом SoC, без облака. Текущая версия ориентирована на **ESP32-S3** и **ESP32-P4**.

Фреймворк построен на **FreeRTOS** с task-based архитектурой. Базовые классы `WhoTaskBase` и `WhoTask` определяют единый жизненный цикл для асинхронных операций.

## Архитектура

ESP-WHO следует многоуровневой архитектуре с ESP-IDF в основе. Использует **Board Support Packages (BSP)** для абстракции аппаратных различий — один и тот же код работает на разных платах (ESP32-S3-EYE, ESP32-P4-Function-EV-Board).

Ключевые компоненты:

- **WhoFrameCap** — конвейер захвата кадров. Узлы `WhoFrameCapNode` соединяются через очереди и биты событий, образуя асинхронный пайплайн.
- **WhoDetect** — потребляет кадры из `WhoFrameCapNode` и запускает модель детекции на базе **ESP-DL** (лица, пешеходы).
- **WhoRecognitionCore** — расширяет детекцию, выполняя извлечение признаков и сопоставление. Управляет внутренним конечным автоматом для операций ENROLL, RECOGNIZE и DELETE.

### Компоненты распознавания

Система распознавания построена как иерархия классов:

- **WhoRecognition** — контейнер и оркестратор. Создаёт задачи детекции (`WhoDetect`) и распознавания (`WhoRecognitionCore`), регистрируя их в `WhoTaskGroup`.
- **WhoRecognitionCore** — основной движок распознавания. Наследует `WhoTask` и работает в цикле, ожидая биты в EventGroup. Обрабатывает события RECOGNIZE, ENROLL, DELETE.
- **HumanFaceRecognizer** — управляет извлечением признаков и базой данных (`face.db`). Методы: `recognize()`, `enroll()`, `delete_last_feat()`.

### Поток данных

Цепочка коллбэков передаёт данные от камеры к движку распознавания:

1. Нажатие кнопки устанавливает бит в EventGroup.
2. `WhoRecognitionCore` заменяет текущий коллбэк детекции на специализированный лямбда-выражение.
3. Когда `WhoDetect` завершает кадр, вызывается лямбда, которая вызывает `m_recognizer->recognize()`.
4. Результат форматируется в строку (например, `"id: 0, sim: 0.85"`) и передаётся в `m_recognition_result_cb`.
5. Коллбэк детекции восстанавливается до исходного состояния.

### Распределение задач по ядрам

- **Детекция**: Core 1, стек 3584 байта.
- **Распознавание**: Core 1, стек 3584 байта.
- **Захват кадров**: Core 0, обработка I/O и прерываний.

## Поддерживаемые сенсоры

Драйвер `esp32-camera` поддерживает следующие сенсоры:

| Сенсор | Макс. разрешение | Тип | Формат вывода |
|--------|-----------------|-----|---------------|
| OV2640 | 1600×1200 | Цветной | YUV, YCbCr422, RGB565, RAW |
| OV3660 | 2048×1536 | Цветной | RAW RGB, RGB565, CCIR656, YCbCr422 |
| OV5640 | 2592×1944 | Цветной | RAW RGB, RGB565, CCIR656, YUV422/420, YCbCr422 |
| OV7670 | 640×480 | Цветной | RAW Bayer, RGB, YUV, RGB565 |
| OV7725 | 640×480 | Цветной | RAW RGB, GRB422, RGB565, YCbCr422 |
| GC2145 | 1600×1200 | Цветной | YUV, YCbCr422, RAW Bayer, RGB565 |
| SC030IOT | 640×480 | Цветной | YUV, YCbCr422, RAW Bayer |
| SC031GS | 640×480 | Моно | RAW MONO, Grayscale |
| HM0360 | 656×496 | Моно | RAW MONO, Grayscale |

**Важно:**

- Для разрешений выше CIF с JPEG требуется **PSRAM**.
- При использовании YUV или RGB нагрузка на чип высока. Рекомендуется захватывать JPEG и конвертировать в RGB через `fmt2rgb888` или `fmt2bmp`.
- При 2+ буферах кадров I2S работает в непрерывном режиме, что удваивает частоту кадров, но увеличивает нагрузку на CPU/память. **Только для JPEG**.

## Установка и сборка

**Требования:** ESP-IDF v5.5.x.

1. Клонируйте репозиторий:
   ```bash
   git clone --recursive https://github.com/espressif/esp-who.git
   ```

2. Установите переменную окружения:
   ```bash
   export IDF_EXTRA_ACTIONS_PATH=/path_to_esp-who/tools/
   ```

3. Соберите пример под нужную плату:
   ```bash
   idf.py -DSDKCONFIG_DEFAULTS=sdkconfig.bsp.<board_name> set-target esp32s3
   idf.py build flash monitor
   ```

## Пример: Face Recognition (ESP-IDF)

```cpp
#include "who_recognition.hpp"
#include "who_frame_cap.hpp"

extern "C" void app_main() {
    auto frame_cap = who::frame_cap::get_dvp_frame_cap_pipeline();
    auto recognition = new who::recognition::WhoRecognition(frame_cap);

    recognition->set_recognition_result_cb([](const char* result) {
        ESP_LOGI(TAG, "Recognition: %s", result);
    });

    recognition->run();

    // Пример: ENROLL (добавление лица)
    // recognition->enroll(1);
    // Пример: RECOGNIZE
    // recognition->recognize();
    // Пример: DELETE
    // recognition->delete_feature(1);
}
```

## Поддерживаемые платы

| Плата | SoC | Функции |
|-------|-----|---------|
| ESP32-S3-EYE | ESP32-S3 | Аудио, микрофон, кнопки, камера, LCD (ST7789), IMU, uSD |
| ESP32-S3-Korvo-2 | ESP32-S3 | Аудио, микрофон (ES7210), динамик (ES8311), кнопки, камера, LCD (ILI9341), LED, uSD, тачскрин (TT21100) |
| ESP32-P4 Function EV Board | ESP32-P4 | Аудио, микрофон, динамик, LCD, тачскрин, uSD, MIPI CSI, USB UVC |

## Ресурсы

- [ESP-WHO на GitHub](https://github.com/espressif/esp-who)
- [ESP-DL — библиотека глубокого обучения](https://github.com/espressif/esp-dl)
- [esp32-camera — драйвер камер](https://github.com/espressif/esp32-camera)
- [ESP-WHO: Get started](https://developer.espressif.com/blog/2026/05/esp-who-get-started/)
- [Edge-AI with ESP32-S3 Workshop](https://developer.espressif.com/workshops/edge-ai-with-esp32-s3/)

[← Назад к Roadmap](../README.md) · [↑ Наверх](#top)