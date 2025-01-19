# Atmega-Muisure-Guard-Sensor-8-led-Strip-

The code is designed for monitoring soil moisture levels specifically for lemon plants, which thrive in soil with a moisture level between 40-60%. The system is optimized to reflect this optimal moisture range and visually display the moisture status using a strip of NeoPixel LEDs.

Logic Breakdown: Maximum Moisture Level: The code defines the maximum acceptable soil moisture as 60%. If the moisture level exceeds this threshold (i.e., the soil becomes too wet), the entire strip of NeoPixel LEDs lights up in blue. This serves as a warning that the soil is too wet, and the system helps prevent overwatering. This is done by checking if the moisture value exceeds the 60% threshold, triggering the blue LEDs to indicate this condition.

Minimum Moisture Level: The minimum moisture threshold is set to 20%. If the soil moisture falls below this value (indicating that the soil is too dry), a red LED will start blinking. This alerts you that the plant needs watering.

Optimal Range (40%-60%): The remaining 8 LEDs display the current moisture level in the range of 40-60%. These LEDs will light up in a color gradient that adjusts based on the moisture level, reflecting the soil's current status. A higher moisture level will cause more LEDs to light up, with the colors transitioning from yellow to green as the moisture level improves.

This code ensures that the lemon plant's soil moisture stays within the healthy range, and provides a clear visual representation of the soil's condition.


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
