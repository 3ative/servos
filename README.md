# Using Servos in Home Assistant with ESPHome

Using Servos from ESPHome is quite simple. Check out the 🎬[YouTube tutorial](https://youtu.be/1f2tuS_PxIE) for setting up and options available.
___
### The Basic Setup:

#### 🛠 The `servo:` Component:
```yaml
servo:
  - id: servo1
    output: output1
```

#### ❓ Optional Servo settings and calibration:
```yaml
    auto_detach_time: 5s  # Optional - Default 0 (Always On)
    transition_length: 0s # Optional - Default 0 (Max Speed)
    min_level: 2.7%   # Optional - Default 3%
    idle_level: 7.3%  # Optional - Default 7.5%
    max_level: 12.2%  # Optional - Default 12.5%
```

#### 🛠 The `output:` Component:
```yaml
output:
  - platform: ledc # For ESP8266 use: esp8266_pwm
    id: output1
    pin: [YOUR GPIO]
    frequency: 50 Hz # MUST BE 50Hz
```

#### 🛠 The `number:` (Slider) Component:
```yaml
substitutions:
  name: Servo Tester
```

```yaml
number:
  - platform: template
    id: servoSlider1
    name: $name 1
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
            id(servo1).write(x / 100.0);
```
___
### Advanced Code-y Bits
##### 🎁 ADVANCED Part 1: Use `on_boot:` to set position at Start-up:
```yaml
esphome:
  ...
  on_boot:
    priority: -100
    then:
      - lambda: !lambda |-
          id(servoSlider1).make_call().set_value(0).perform();
```

##### 🎁 ADVANCED Part 2-1: SINE Waving Servo 5:
- Use ``interval:`` to Calculate Sine Values:
```yaml
globals:
  - id: sine_wave_phase
    type: float
    restore_value: no
    initial_value: '0.0'

  - id: sine_wave_range
    type: float
    restore_value: no
    initial_value: '0.0'
  
  - id: sine_wave_speed
    type: float
    restore_value: no
    initial_value: '1.0'

interval:
  - interval: 40ms
    then:
      if:
        condition:
          switch.is_on: sinEnable
        then:
          - lambda: |-
              auto t = id(sine_wave_phase);
              auto range = id(sine_wave_range);
              auto speed = id(sine_wave_speed);

              // Increment phase
              t += (2 * PI) / speed;
              if (t >= 2 * PI) t = 0; // Reset the phase after a full cycle

              id(sine_wave_phase) = t;
              int value = round(range * sin(t));

              id(servoSlider5).publish_state(value);

switch:
  - platform: template
    id: sinEnable
    name: $name 5 Enable Sine
    optimistic: true
```

##### 🎁 ADVANCED Part 2-2: SINE Waving Servo 5:
- The Two Extra `number:` Components to control the SINE Values :
```yaml
  - platform: template
    id: servoRange
    name: $name 5 Range
    min_value: 0
    max_value: 100
    initial_value: 0
    step: 5
    optimistic: True
    on_value: 
      then:
        - lambda: !lambda |-
            id(sine_wave_range) = x;

  - platform: template
    id: servoSpeed
    name: $name 5 Speed
    min_value: 0
    max_value: 100
    initial_value: 5
    step: 1
    optimistic: True
    on_value: 
      then:
        - lambda: !lambda |-
            id(sine_wave_speed) = 5 + (100-x);
```
##### 🎁 ADVANCED Part 2-3: SINE Waving Servo 5:
- Stop Spamming the ``logger:``:
```yaml
logger:
  logs:
    number: none
    servo: none
```
---
### 🤝 Found this useful, want to say 'Thanks' and support my efforts. CHEERS🍺
| Buy me a Coffee | PATREON |
|-----------------|---------|
| [![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-donate-yellow.svg?style=flat-square&logo=buy-me-a-coffee)](https://www.buymeacoffee.com/3ative) | [![Patreon](https://img.shields.io/badge/Patreon-support-red.svg?style=flat-square&logo=patreon)](https://www.patreon.com/3ative) |
---
