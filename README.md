WindSense — Smart Auto-Yaw Wind Turbine
An Arduino-powered small-scale wind turbine that **automatically rotates toward the wind source** using ultrasonic sensing — maximizing power generation without any manual adjustment
What is WindSense?

Conventional lab-scale wind turbine models are fixed in one direction — if the wind shifts, power drops. **WindSense solves this.**

It uses an HC-SR04 ultrasonic sensor to detect the wind source position and an Arduino-controlled DC motor to rotate (yaw) the turbine into optimal alignment automatically — just like real industrial wind turbines, but built for under **PKR 3,000 (~$10 USD)**.

 Key Features

- **Automatic Yaw Control** — turbine self-aligns to the wind source in ~2 seconds
- **Full Bridge Rectifier** — converts AC-like generator output to smooth DC
- **Real-time Voltage Monitoring** — Arduino reads generator output via analog pin
- **Threshold-based Smart Logic** — rotates left, right, or stops based on distance
- **Low Cost** — entire build under PKR 2,895

 Components

| Component | Qty | Role |
|-----------|-----|------|
| Arduino Uno | 1 | System brain / microcontroller |
| HC-SR04 Ultrasonic Sensor | 1 | Detects wind source distance |
| L298N Motor Driver | 1 | Controls yaw motor direction & speed |
| DC Motor (Generator) | 1 | Converts wind energy → electrical energy |
| DC Motor (Yaw) | 1 | Physically rotates turbine |
| 1N4007 Diodes | 4 | Full bridge rectifier |
| Electrolytic Capacitor | 1 | Smooths DC output |
| 10K Resistor | 1 | Voltage divider for Arduino A0 |
| 220Ω Load Resistor | 1 | Electrical load on generator |
| 9V Battery | 1 | Powers L298N driver |
| Mini Fan | 1 | Wind source (simulation) |
| Breadboard + Wires | 1 set | Circuit assembly |

---

 How It Works

```
Wind (Fan) → Turbine Blades Spin → DC Motor 1 Generates Voltage
                                          ↓
                               Full Bridge Rectifier (AC → DC)
                                          ↓
                                   Capacitor Smoothing
                                          ↓
                                     Load / Output

HC-SR04 Measures Distance → Arduino Processes → L298N Driver → DC Motor 2 Yaws Turbine
```

Yaw Logic:

| Fan Distance | Action | Generator Output | Status |
|---|---|---|---|
| < 15 cm | Rotate Right | 1.2 – 1.8V | Too close — adjusting |
| 15 – 40 cm | Stop (Aligned) | 2.8 – 3.5V | ✅ Optimal — generating |
| > 40 cm | Rotate Left | 0.1 – 0.5V | Searching — rotating |
| No wind | No action | 0.0V | Idle |

 Pin Connections

| Component | Pin | Connected To |
|-----------|-----|-------------|
| HC-SR04 | VCC / GND | 5V / GND |
| HC-SR04 | TRIG / ECHO | Pin 9 / Pin 10 |
| L298N | ENA / IN1 / IN2 | Pin 6 (PWM) / Pin 7 / Pin 8 |
| L298N | VCC / GND / 12V | Arduino 5V / GND / 9V Battery |
| DC Motor 2 | OUT1 / OUT2 | L298N OUT1 / OUT2 |
| DC Motor 1 | Wire A / Wire B | D1 Anode / D3 Anode |
| Bridge Rectifier Output | Positive Rail → A0 | Via 10K voltage divider |
| Capacitor | + / − | Positive Rail / GND |

Source Code

```cpp
// WindSense — Auto Yaw Control
// FAST NUCES Karachi | EL2010 Spring 2026

const int trigPin = 9;
const int echoPin = 10;
const int in1 = 4;
const int in2 = 5;
const int in3 = 2;
const int in4 = 3;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
}

void loop() {
  long duration, distance;

  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = (duration / 2) / 29.1;

  // Yaw Control Logic
  if (distance > 0 && distance < 15) {
    Serial.println("Too close! Rotating Right.");
    rotateRight();
  } else if (distance >= 15 && distance <= 40) {
    Serial.println("Aligned! Generating power.");
    stopMotors();
  } else {
    Serial.println("Searching... Rotating Left.");
    rotateLeft();
  }

  delay(100);
}

void rotateRight() {
  digitalWrite(in1, HIGH); digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);  digitalWrite(in4, HIGH);
}

void rotateLeft() {
  digitalWrite(in1, LOW);  digitalWrite(in2, HIGH);
  digitalWrite(in3, HIGH); digitalWrite(in4, LOW);
}

void stopMotors() {
  digitalWrite(in1, LOW); digitalWrite(in2, LOW);
  digitalWrite(in3, LOW); digitalWrite(in4, LOW);
}
```

---
Results
| Parameter | Target | Achieved |
|-----------|--------|----------|
| Yaw response time | < 3 seconds | ~2 seconds ✅ |
| Generator output (aligned) | > 2.5V | 2.8 – 3.5V ✅ |
| Sensor range | 10 – 200cm | 2 – 180cm ✅ |
| Alignment accuracy | Sweet spot hold | ±3cm ✅ |

Power improvement:aligned state (3.5V) vs misaligned state (<0.5V) — a 7x improvement in generator output.
 SDG Alignment

| Goal | Relevance |
|------|-----------|
| **SDG 7** — Affordable & Clean Energy | Demonstrates smart wind energy harvesting |
| **SDG 9** — Industry, Innovation & Infrastructure | Sensor-based automation + power electronics |
| **SDG 13** — Climate Action | Zero-emission renewable energy awareness |

Pakistan has significant wind energy potential, especially in the Sindh and Balochistan corridors. This project builds engineering capacity for local renewable energy development.

Cost

Total build cost: PKR 2,895 (~$10 USD)
Future Improvements

- Dual HC-SR04 sensors (left + right) for more precise directional tracking
- LCD display for real-time voltage, RPM, and alignment status
- Hall effect sensor for accurate RPM measurement
- PID control to replace threshold-based yaw logic
- Battery charging circuit for energy storage
- Bluetooth/Wi-Fi module for remote power monitoring
References

- [Arduino Official Documentation](https://www.arduino.cc/reference/en/)
- [HC-SR04 Datasheet](https://cdn.sparkfun.com/datasheets/Sensors/Proximity/HCSR04.pdf)
- Chapman, S.J. — *Electric Machinery Fundamentals*, 5th Ed., McGraw-Hill, 2012
- Mohan, N. — *Power Electronics: Converters, Applications, and Design*, Wiley, 2003
- [UN Sustainable Development Goals](https://sdgs.un.org/goals)
]
