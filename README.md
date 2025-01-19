# Atmega-Muisure-Guard-Sensor-8-led-Strip-
+----------------------------------+
|          INITIALIZATION         |
|            Setup()              |
+----------------------------------+
                |
                v
+----------------------------------+
|    Initialize EEPROM            |
|    readEEPROM() - Load config   |
+----------------------------------+
                |
                v
+----------------------------------+
|    Connect to WiFi              |
|    Connection attempt (3s)      |
+----------------------------------+
                |
                v
+-------------[o]------------------+
|    WiFi Connected?              |
+----------------------------------+
        |              |
     Yes|           No|
        v              v
+--------------+ +--------------+
|  Start NTP   | |  Offline Mode |
|  & RTC Sync  | |              |
+--------------+ +--------------+
        |              |
        +------+-------+
                |
                v
+==================================+
|            MAIN LOOP            |
+==================================+
                |
+-------------[o]------------------+
|    Check time (RTC)             |
|    Hour: 6-24 vs 0-6            |
+----------------------------------+
        |              |
    Day |           Night|
        v              v
+--------------+ +--------------+
| Day Mode     | | Night Mode   |
| PWM=220      | | PWM=10       |
+--------------+ +--------------+
        |              |
        +------+-------+
                |
                v
+----------------------------------+
|    Check Motion Sensor          |
|    digitalRead(PIN_SENSOR)      |
+----------------------------------+
                |
                v
+-------------[o]------------------+
|    Motion Detected?             |
+----------------------------------+
        |              |
     Yes|           No|
        v              v
+--------------+ +--------------+
| Brighten LED | | Check Time   |
| 5s to max PWM| | Since Motion |
+--------------+ +--------------+
        |              |
        |              v
        |    +----------------[o]----------------+
        |    | Time > MOVEMENT_TIME_THRESHOLD?   |
        |    +---------------+------------------+
        |             |              |
        |         Yes|           No|
        |             v              v
        |    +--------------+ +--------------+
        |    | Go to PWM_STANDBY| | Keep Current PWM|
        |    +--------------+ +--------------+
        |             |              |
        +-------------+------+-------+
                             |
                             v
+----------------------------------+
|    Check Time from last NTP Sync |
+----------------------------------+
                |
                v
+-------------[o]------------------+
|    Difference > 10s or 24h?     |
+----------------------------------+
        |              |
     Yes|           No|
        v              |
+--------------+       |
| Sync RTC from |       |
| NTP           |       |
+--------------+       |
        |              |
        +------+-------+
                |
                v
+----------------------------------+
|    Handle Web Server Requests   |
|    handleRoot() & Requests       |
+----------------------------------+
                |
                v
+-------------[o]------------------+
|    Request for Update?          |
+----------------------------------+
        |              |
     Yes|           No|
        v              |
+--------------+       |
| Save Config  |       |
| to EEPROM    |       |
+--------------+       |
        |              |
        +------+-------+
                |
                +---------------------------------+
