# Подключение плат ESP32 в Arduino

Для того, чтобы добавить поддержку плат ESP32 в Arduino IDE, необходимо подключить библиотеку [Arduino-ESP32](https://github.com/espressif/arduino-esp32). Для этого откройте "Параметры"(Ctrl + ,).

![параметры](/pic/arduino01.png)

В окне "Дополнительные ссылки для Менеджера плат" вставльте ссылку ниже. 

`https://espressif.github.io/arduino-esp32/package_esp32_index.json`

Откройте "Менеджер плат"(Ctrl + Shift + B). В строке поиска введите `esp32`, установите библитеку от Espressif Systems.

![менеджер плат](/pic/arduino02.png)

[Назад](/README.md)