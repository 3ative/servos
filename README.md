# Using Servos in Home Assistant with ESPHome

Using Servos from ESPHome is quite simple. Check out the 🎬[YouTube tutorial](https://youtu.be/YmqtMTO5NVc) for setting up and options available.
___
###### 🛠 Number (Slider) Component:
```yaml
number:
  - platform: template
    id: servoSlider1
    name: Servo Tester 1
    icon: mdi:sync
    optimistic: true
    min_value: -100
    max_value: 100
    initial_value: 0
    step: 5
    on_value:
      then:
        - lambda: !lambda |-
            // Servo Write range is: -1.0 to 1.0, so divide by 100
            id(servo_1).write(x / 100.0);
```
###### 🛠 Servo Component:
```yaml
servo:
  - id: servo_1
    output: output1
```

###### ❓ Optional Servo settings and calibration:
```yaml
    auto_detach_time: 5s  # Optional - Default 0 (Always On)
    transition_length: 0s # Optional - Default 0 (Max Speed)
    min_level: 2.7%   # Optional - Default 3%
    idle_level: 7.3%  # Optional - Default 7.5%
    max_level: 12.2%  # Optional - Default 12.5%
```
###### 🛠 Output Component:
```yaml
output:
  - platform: ledc # For ESP8266 use: esp8266_pwm
    id: output1
    pin: [YOUR GPIO]
    frequency: 50 Hz # MUST BE 50Hz
```
---
###### 🛠 Advanced: Set position at Start-up (Boot):
```yaml
esphome:
  ...
  on_boot:
    priority: -100
    then:
      - lambda: !lambda |-
          id(servoSlider1).make_call().set_value(0).perform();
```
___

#### 💖 Found this useful, want to say '*Thanks*' and support my efforts. *CHEERS*🍺
| Buy me a Coffee | PATREON |
|-----------------|---------|
| https://www.buymeacoffee.com/3ative | https://www.patreon.com/3ative |
