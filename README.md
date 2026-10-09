# MQTT Data Dashboard

Broker-оор дамжин ирсэн өгөгдлийн мэдээллийг бодит хугацаанд дүрслэн харуулах болон LED төлөвийг зайнаас удирдах вэб дашбоард. 

Вэб хөтөч нь стандарт TCP порттой шууд харьцдаггүй тул broker дээр WebSockets буюу **9001** портыг нээж өгөх шаардлагатай.

---

## 1. Broker тохируулах ба асаах

### Тохиргооны файл бэлтгэх
Терминал дээр дараах командыг ажиллуулан тохиргооны файлыг үүсгэнэ:

```bash
cat << 'EOF' > ~/Desktop/mosquitto-lab.conf
listener 1883 0.0.0.0
allow_anonymous true
persistence false

listener 9001 0.0.0.0
protocol websockets
EOF
```

### Broker-ийг асаах
```bash
mosquitto -c ~/Desktop/mosquitto-lab.conf -v
```

---

## 2. Архитектур ба Ажиллах зарчим

Систем нь **Publish / Subscribe** загвараар төвлөрсөн MQTT Broker-оор дамжин өгөгдөл солилцоно[cite: 1]:

1. **ESP32 -> Broker -> Dashboard / Phone:**
   * ESP32 нь DHT11 мэдрэгчээс 5 секунд тутам уншсан температур, агаарын чийгшил болон LED төлөвийг JSON хэлбэрээр `team1/msg` topic руу илгээнэ (Publish)[cite: 1].
2. **Dashboard / Phone -> Broker -> ESP32:**
   * Хэрэглэгчийн удирдлагын хэсгээс `team1/led` topic руу `ON` эсвэл `OFF` комманд илгээхэд ESP32-ийн GPIO 2 хөлийн LED төлөв өөрчлөгдөнө[cite: 1].

---

## 3. Системийн үзүүлэлтүүд ба Тохиргоо

- **MQTT Broker:** Eclipse Mosquitto[cite: 1]
- **Портууд:** 
  - `1883` — Стандарт TCP MQTT (ESP32 болон гар утасны апп-д)[cite: 1]
  - `9001` — WebSockets (Вэб дашбоардад)
- **MQTT Topics:**
  - Telemetry (Мэдрэгчийн өгөгдөл): `team1/msg`
  - Control (Гэрэл удирдах): `team1/led`
- **JSON Payload формат:**
  ```json
  {
    "temperature": 28,
    "humidity": 7,
    "led": 0
  }
  ```
