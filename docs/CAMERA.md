# Камеры и компьютерное зрение на ESP32

> Отдельная страница Roadmap ESP32. Здесь собрано всё, что нужно для начала работы с камерами и CV на ESP32: аппаратные интерфейсы, сенсоры, библиотеки, примеры кода, Edge AI и реальные проекты.

---

## Содержание

- [Зачем камера на микроконтроллере](#зачем-камера-на-микроконтроллере)
- [Аппаратные интерфейсы](#аппаратные-интерфейсы)
- [Сенсоры](#сенсоры)
- [Выбор платформы](#выбор-платформы)
- [Программное обеспечение](#программное-обеспечение)
- [Пример кода: CameraWebServer](#пример-кода-camerawebserver)
- [Компьютерное зрение на устройстве (Edge AI)](#компьютерное-зрение-на-устройстве-edge-ai)
- [Проекты](#проекты)
- [Полезные ссылки](#полезные-ссылки)

---

## Зачем камера на микроконтроллере

Классический подход к компьютерному зрению предполагает отправку изображений на сервер или в облако, где они обрабатываются мощным GPU. Это даёт высокую точность, но создаёт задержку, требует постоянного подключения к сети и поднимает вопросы приватности.

**Edge AI (периферийный ИИ)** переносит вывод нейросети прямо на устройство. Это даёт:

- **Низкую задержку** — решение принимается за миллисекунды, а не за время сетевого round-trip.
- **Приватность** — сырые изображения не покидают устройство.
- **Автономность** — работает без интернета.
- **Низкую стоимость** — нет расходов на облачные вычисления и передачу данных.

Ограничения микроконтроллеров очевидны: у ESP32-S3 всего 512 КБ SRAM (плюс до 8 МБ PSRAM), тогда как облачный сервер имеет гигабайты RAM и гигагерцовые многоядерные CPU. Поэтому модели приходится квантовать (обычно из 32-битных float в 8-битные INT8), что уменьшает память в 4 раза при минимальной потере точности.

---

## Аппаратные интерфейсы

Камера подключается к SoC через **шину управления** и **шину пиксельных данных**.

- **SCCB (Serial Camera Control Bus)** — по сути I2C. Хост использует её для обнаружения сенсора, загрузки регистров и изменения усиления, экспозиции, зеркалирования, качества JPEG в реальном времени.
- **Пиксельная шина** — сюда приходит само изображение.

### DVP (Digital Video Port)

Параллельный интерфейс. Сенсор выдаёт 8 бит данных (D0–D7) одновременно, плюс сигналы VSYNC (вертикальная синхронизация), DE/HREF (горизонтальная синхронизация), PCLK (тактовая частота пикселей) и XCLK (внешний клок от SoC). Данные проходят через асинхронный FIFO (до 16 байт буфера), затем преобразуются в RGB или YCbCr.

**Особенности:**

- Поддерживается на ESP32, ESP32-S2, ESP32-S3, ESP32-P4.
- У ESP32-S3 **отдельный CAM DVP интерфейс** с частотой периферии в 2–3 раза выше, чем у классического ESP32.
- Максимальное разрешение: до 5 МП при выводе JPEG; до 1 МП для YUV422/RGB565 (из-за ограничений DVP и DMA).

### MIPI CSI

Последовательный высокоскоростной интерфейс, стандарт для мобильных камер. Доступен на **ESP32-P4**, который также поддерживает DVP.

**Преимущества MIPI CSI:**

- Высокое разрешение (до 1080p и выше).
- Меньше проводов.
- Высокая пропускная способность.

**Недостатки:**

- Требует MIPI PHY на стороне SoC.
- Сложнее в разводке и настройке.

ESP32-P4 имеет **интегрированный ISP (Image Signal Processor)**, который выполняет дебайеризацию, шумоподавление, авто-баланс белого и другие операции до того, как кадр попадёт в память.

### SPI

Низкоскоростной интерфейс для камер с максимальным разрешением 320×240. Прост в подключении, но подходит только для простых задач.

---

## Сенсоры

Библиотека `esp32-camera` поддерживает широкий спектр сенсоров. Ключевые модели:

| Сенсор | Макс. разрешение | Цвет | Форматы | Размер пикселя | Примечание |
|--------|-----------------|------|---------|---------------|-----------|
| **OV2640** | 1600×1200 (2 МП) | Цветной | YUV, YCbCr422, RGB565, RAW | 1/4" | Классика ESP32-CAM, дёшев |
| **OV3660** | 2048×1536 (3 МП) | Цветной | RAW RGB, RGB565, CCIR656, YCbCr422 | 1/5" | Лучше в слабом освещении, рекомендуется для AI |
| **OV5640** | 2592×1944 (5 МП) | Цветной | RAW RGB, RGB565, CCIR656, YUV, JPEG | 1/4" | Высокое разрешение, автофокус |
| **OV7670** | 640×480 (VGA) | Цветной | RAW Bayer, YUV, RGB565 | 1/6" | Старый, дёшев |
| **OV7725** | 640×480 (VGA) | Цветной | RAW RGB, RGB565, YCbCr422 | 1/4" | Быстрый VGA-сенсор |
| **GC2145** | 1600×1200 (2 МП) | Цветной | YUV, YCbCr422, RAW, RGB565 | 1/5" | Альтернатива OV2640 |
| **SC030IOT** | 640×480 (VGA) | Цветной | YUV, YCbCr422, RAW Bayer | 1/6.5" | Для ESP32-S3 (DVP) |

Полный список поддерживаемых сенсоров включает OV3640, NT99141, GC032A, GC0308, BF3005, BF20A6, SC101IOT, SC031GS (моно), HM0360 (моно), HM1055.

**Важно:** для разрешений выше CIF с JPEG требуется **PSRAM**. При использовании YUV или RGB без JPEG нагрузка на чип высока, и данные могут теряться, особенно при включённом Wi-Fi. Рекомендуется захватывать JPEG и конвертировать в RGB через `fmt2rgb888` или `fmt2bmp`.

---

## Выбор платформы

| Чип | Интерфейс камеры | AI-ускорение | JPEG-кодек | PPA (2D) | ISP | Wi-Fi |
|-----|-----------------|-------------|-----------|---------|-----|-------|
| **ESP32** | DVP | Нет | Нет | Нет | Нет | Wi-Fi 4 |
| **ESP32-S3** | DVP | 128-bit SIMD (PIE) | Нет | Нет | Нет | Wi-Fi 4 |
| **ESP32-P4** | DVP + MIPI-CSI | PIE + 128-bit SIMD | **Аппаратный** | **Да** | **Да** | Нет (через сопроцессор) |
| **ESP32-S31** | DVP (16-bit) | 128-bit SIMD | **Аппаратный** | **Да** | — | Wi-Fi 6 |

Для **простых задач** (стриминг, детекция движения) достаточно ESP32-CAM на ESP32 или ESP32-S3.

Для **Edge AI** (распознавание лиц, классификация объектов) оптимален **ESP32-S3** — векторные инструкции PIE ускоряют нейросетевые вычисления.

Для **профессиональных задач** с высоким разрешением и MIPI CSI — **ESP32-P4** с аппаратным ISP и JPEG-кодеком.

---

## Программное обеспечение

### esp32-camera

Основная библиотека для работы с камерами на ESP32/ESP32-S2/ESP32-S3. Поддерживает DVP-интерфейс, драйверы сенсоров и кодирование/декодирование изображений.

**Установка (ESP-IDF):**

```bash
idf.py add-dependency "espressif/esp32-camera"
```

**Установка (Arduino):** доступна через Library Manager.

### ESP_Video (Arduino)

Новая библиотека в Arduino core для ESP32, которая оборачивает ESP-IDF-компонент `esp_video` и предоставляет **V4L2-подобный API** для MIPI-CSI и DVP. Работает на ESP32-S3 и ESP32-P4 при сборке с ESP-IDF 5.4.0+.

**Ключевые концепции:**

- Устройство открывается как `/dev/video0`.
- Запрос MMAP-буферов.
- Кадры извлекаются в цикле.
- Поддерживаются live preview, захват кадров, JPEG-снимки, настройка сенсора (gain, exposure, flip).

### ESP-WHO

Фреймворк для распознавания лиц и обнаружения объектов от Espressif. Целевые чипы: **ESP32-S3** и **ESP32-P4**.

### ESP-DL

Библиотека для глубокого обучения на ESP32. Содержит оптимизированные слои нейросетей, квантованные модели и утилиты.

---

## Пример кода: CameraWebServer

Классический пример из Arduino IDE: `File → Examples → ESP32 → Camera → CameraWebServer`. Он поднимает веб-сервер, через который можно смотреть поток с камеры.

### Минимальный скетч (Arduino)

```cpp
#include "esp_camera.h"
#include <WiFi.h>

// Выберите модель камеры (раскомментируйте нужную)
#define CAMERA_MODEL_AI_THINKER  // ESP32-CAM
#include "camera_pins.h"

const char* ssid = "ваш_SSID";
const char* password = "ваш_пароль";

void startCameraServer();

void setup() {
  Serial.begin(115200);
  Serial.setDebugOutput(true);
  Serial.println();

  camera_config_t config;
  config.ledc_channel = LEDC_CHANNEL_0;
  config.ledc_timer   = LEDC_TIMER_0;
  config.pin_d0       = Y2_GPIO_NUM;
  config.pin_d1       = Y3_GPIO_NUM;
  config.pin_d2       = Y4_GPIO_NUM;
  config.pin_d3       = Y5_GPIO_NUM;
  config.pin_d4       = Y6_GPIO_NUM;
  config.pin_d5       = Y7_GPIO_NUM;
  config.pin_d6       = Y8_GPIO_NUM;
  config.pin_d7       = Y9_GPIO_NUM;
  config.pin_xclk     = XCLK_GPIO_NUM;
  config.pin_pclk     = PCLK_GPIO_NUM;
  config.pin_vsync    = VSYNC_GPIO_NUM;
  config.pin_href     = HREF_GPIO_NUM;
  config.pin_sccb_sda = SIOD_GPIO_NUM;
  config.pin_sccb_scl = SIOC_GPIO_NUM;
  config.pin_pwdn     = PWDN_GPIO_NUM;
  config.pin_reset    = RESET_GPIO_NUM;
  config.xclk_freq_hz = 20000000;
  config.pixel_format = PIXFORMAT_JPEG;

  // Настройки качества
  if (psramFound()) {
    config.frame_size   = FRAMESIZE_UXGA;  // 1600x1200
    config.jpeg_quality = 10;
    config.fb_count     = 2;
  } else {
    config.frame_size   = FRAMESIZE_SVGA;  // 800x600
    config.jpeg_quality = 12;
    config.fb_count     = 1;
  }

  // Инициализация
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Ошибка инициализации камеры: 0x%x", err);
    return;
  }

  // Подключение к Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWi-Fi подключён");
  Serial.print("IP: ");
  Serial.println(WiFi.localIP());

  startCameraServer();
  Serial.println("Веб-сервер запущен. Откройте IP в браузере.");
}

void loop() {
  delay(10000);
}
```

**Что происходит:**

1. Настраиваются пины DVP для конкретной платы.
2. Выбирается формат пикселей (JPEG — самый эффективный для Wi-Fi стриминга).
3. При наличии PSRAM выбирается высокое разрешение и двойная буферизация.
4. Камера инициализируется, поднимается Wi-Fi и веб-сервер.

При использовании `fb_count = 2` (два буфера кадров) I2S работает в непрерывном режиме, и каждый кадр помещается в очередь. Это удваивает частоту кадров, но требует больше памяти. **Рекомендуется только с JPEG**.

### Пример на ESP-IDF (V4L2-style, ESP32-P4)

```c
#include "esp_video_init.h"
#include "linux/videodev2.h"

// Открытие устройства
int fd = open("/dev/video0", O_RDWR);
if (fd < 0) {
    ESP_LOGE(TAG, "Не удалось открыть /dev/video0");
    return;
}

// Запрос формата
struct v4l2_format fmt = { .type = V4L2_BUF_TYPE_VIDEO_CAPTURE };
ioctl(fd, VIDIOC_G_FMT, &fmt);
fmt.fmt.pix.width  = 1920;
fmt.fmt.pix.height = 1080;
fmt.fmt.pix.pixelformat = V4L2_PIX_FMT_RGB565;
ioctl(fd, VIDIOC_S_FMT, &fmt);

// Запрос буферов
struct v4l2_requestbuffers req = {
    .count = 2,
    .type  = V4L2_BUF_TYPE_VIDEO_CAPTURE,
    .memory = V4L2_MEMORY_MMAP
};
ioctl(fd, VIDIOC_REQBUFS, &req);

// Захват и вывод
for (int i = 0; i < 10; i++) {
    struct v4l2_buffer buf = { .type = V4L2_BUF_TYPE_VIDEO_CAPTURE, .memory = V4L2_MEMORY_MMAP };
    ioctl(fd, VIDIOC_DQBUF, &buf);  // Извлечь кадр
    // ... обработка буфера ...
    ioctl(fd, VIDIOC_QBUF, &buf);  // Вернуть буфер
}
```

---

## Компьютерное зрение на устройстве (Edge AI)

### Что можно запустить на ESP32-S3

| Задача | Модель | Время инференса | Примечание |
|--------|--------|----------------|-----------|
| **Детекция лиц** | ESP-WHO (MSR + MNP) | ~37–40 мс на кадр | На ESP32-S3-EYE |
| **Классификация изображений** | MobileNetV1 (INT8) | Зависит от разрешения | 64×64 → ~6.3 FPS на XIAO ESP32-S3 |
| **Распознавание жестов** | Custom CNN | — | ESP-WHO |
| **Обнаружение объектов** | TFLite Micro | — | Квантование обязательно |
| **Классификация болезней растений** | MobileNetV2 (INT8) | — | Tensor Arena 220 КБ в PSRAM |

### Инструменты

- **TensorFlow Lite for Microcontrollers (TFLite Micro)** — стандартный фреймворк для запуска моделей на MCU.
- **ESP-DL** — оптимизированные слои и операторы для ESP32-S3/P4.
- **Edge Impulse** — облачная платформа для сбора данных, обучения и экспорта моделей под ESP32.
- **ESP-WHO** — готовые модели для лиц.
- **ESP-SR** — распознавание речи (не CV, но часто используется вместе).

### Оптимизация модели

1. **Квантование (INT8)** — обязательно для ESP32. Уменьшает модель в 4 раза, ускоряет вычисления.
2. **Прунинг** — удаление малозначимых весов.
3. **Выбор разрешения** — 96×96 или 128×128 обычно достаточно для классификации; 320×240 — для детекции.
4. **Использование PSRAM** — Tensor Arena выносится в PSRAM, освобождая SRAM.

---

## Проекты

### Камеры и стриминг

| Проект | Описание | Ссылка |
|--------|----------|--------|
| **ESP32-CAM WebServer** | Расширенная веб-камера с настройкой через браузер и MJPEG-потоком | [GitHub](https://github.com/easytarget/esp32-cam-webserver) |
| **esp32-cam-rtsp** | RTSP-сервер для ESP32-CAM с настраиваемым разрешением | [GitHub](https://github.com/rzeldent/esp32cam-rtsp) |
| **Esp32Cam-DoorBell** | Умный дверной звонок с отправкой фото в Telegram и email | [GitHub](https://github.com/kXborg/Esp32Cam-DoorBell) |
| **ESP32-AI-CAMERA** | Умная камера на ESP32-S3 с LVGL и AI-обработкой на устройстве | [GitHub](https://github.com/tejaspatelll/ESP32-AI-CAMERA) |

### Компьютерное зрение и AI

| Проект | Описание | Ссылка |
|--------|----------|--------|
| **ESP-WHO** | Официальный фреймворк Espressif для распознавания лиц | [GitHub](https://github.com/espressif/esp-who) |
| **edge-impulse-esp32-cam** | Классификация изображений через Edge Impulse | [GitHub](https://github.com/alankrantas/edge-impulse-esp32-cam-image-classification) |
| **tensorflow-model-flask** | Система распознавания лиц: ESP32-CAM → Flask → OpenCV + TensorFlow | [GitHub](https://github.com/Infineon-X/tensorflow-model-flask) |
| **reconocimiento_facial_opencv_esp32_cam** | Видеонаблюдение с распознаванием лиц через OpenCV | [GitHub](https://github.com/joellinder/reconocimiento_facial_opencv_esp32_cam) |

### Научные работы и исследования

| Работа | Тема | Ссылка |
|--------|------|--------|
| **Optimized embedded AI: CNNs on ESP32-CAM** | Классификация Fashion MNIST, 92.3% точности на ESP32-CAM | [ACM](https://dlnext.acm.org/doi/10.1007/s00607-025-01559-z) |
| **Quantized MobileNetV2 for Tomato Leaf Disease** | Классификация болезней томатов на ESP32-CAM за $7 | [Zenodo](https://zenodo.org) |
| **TinyML Safety Helmet Detection** | Детекция касок на ESP32-S3-EYE, сжатие модели с 36 МБ до 55 КБ | [IEEE](https://ieeexplore.ieee.org) |
| **Real-Time Classroom Student Monitoring** | Мониторинг студентов с ESP32-CAM и CNN | [IEEE](https://ieeexplore.ieee.org) |

---

## Полезные ссылки

### Официальные ресурсы

- [esp32-camera (GitHub)](https://github.com/espressif/esp32-camera) — драйвер камер
- [ESP-Video Components](https://docs.espressif.com/projects/esp-video-components/en/latest/) — документация
- [ESP-WHO](https://github.com/espressif/esp-who) — распознавание лиц
- [ESP-DL](https://github.com/espressif/esp-dl) — глубокое обучение
- [ESP32-S3-EYE Getting Started](https://github.com/espressif/esp-who/blob/master/docs/en/get-started/ESP32-S3-EYE_Getting_Started_Guide.md)
- [ESP32-P4-Function-EV-Board](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32p4/esp32-p4-function-ev-board/user_guide.html)

### Библиотеки и фреймворки

- [TensorFlow Lite Micro](https://www.tensorflow.org/lite/microcontrollers)
- [Edge Impulse](https://edgeimpulse.com/)
- [Arduino ESP32 core](https://github.com/espressif/arduino-esp32)
- [ESP_Video (Arduino)](https://github.com/espressif/arduino-esp32/tree/master/libraries/ESP_Video)

### Обучающие материалы

- [Using ESP_Video in Arduino](https://developer.espressif.com/blog/2026/09/arduino-esp-video-camera-capture/) — официальный блог
- [Edge-AI with ESP32-S3 Workshop](https://developer.espressif.com/workshops/edge-ai-with-esp32-s3/introduction/)
- [ESP32 Camera Modules Compared](https://www.espboards.dev) — сравнение сенсоров
- [Simple Arduino ESP32 Camera Demo](https://github.com/nodematrix/ESP32CameraDemo)

### Покупка

- **ESP32-CAM (AI-Thinker)** — самая дешёвая плата с OV2640, ~$5–7
- **ESP32-S3-EYE** — официальная плата для AI, OV2640/OV3660, микрофон, LCD
- **XIAO ESP32-S3 Sense** — компактная плата с камерой OV2640 и микрофоном, ~$15–40
- **FireBeetle 2 ESP32-P4** — MIPI CSI/DSI, 1080p, H.264

---

*Последнее обновление: октябрь 2026. Если найдёте неточность или хотите добавить проект — правьте смело.*