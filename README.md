# HexTech Circuit Board User Manual

Welcome to the HexTech Circuit Board User Manual. This guide will help you get started with the HexTech circuit board, a versatile platform for building and automating various projects.

## Table of Contents
- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [Installation](#installation)
- [Using the HexTech Library](#using-the-hextech-library)
- [Connecting to MQTT](#connecting-to-mqtt)
- [Programming with HexTech](#programming-with-hextech)
- [Example Projects](#example-projects)
- [Troubleshooting](#troubleshooting)
- [Conclusion](#conclusion)

## Introduction
The HexTech circuit board is designed for engineers and hobbyists who want to explore the world of electronics and automation. With built-in LEDs and the ability to control stepper motors, DC motors, BLDC motors, MOSFETs, switches, sensors, solenoids, and more, HexTech is a powerful and user-friendly tool for a wide range of projects. Whether you're looking to automate your home, build a robot, or control devices remotely, HexTech provides the flexibility and ease of use needed to bring your ideas to life.

## Getting Started
### What You Need
- HexTech circuit board starter kit:
  - HexTech circuit board (provided)
  - Power supply (provided)
  - Power cables (provided)
- Motors (stepper, DC, BLDC), switches, sensors, solenoids, and other peripherals as needed

## Installation
To use the HexTech board with Python, you need to set up the HexTech library. Follow these steps:

1. Clone the repository:
   ```sh
   git clone https://github.com/your-username/hextech.git
   cd hextech
2. Install the required Python packages:
   ```sh
    pip install pyserial keyboard paho-mqtt


## Using the HexTech Library

To get started with programming the HexTech board, import the `HexTechMusclev1` class from the library:

    ```{python}
    from hextech import HexTechMusclev1

    hex = HexTechMusclev1("COM10")
    hex.blue_led.turn_on()


## Connecting to MQTT

Connect to HexTech from your computer using MQTT. Here is an example of how to connect to the HexTech board through Python using the `paho-mqtt` library:

    ```{python}
import time
import paho.mqtt.client as mqtt

USERNAME = "hextech-andrea"
PASSWORD = "andrea"
commands = "hextech/hextech-andrea/commands"
topic = "hextech/hextech-andrea/status"

def on_publish(client, userdata, mid, reason_code, properties):
    try:
        userdata.remove(mid)
        print("Published")
    except KeyError:
        print("on_publish() is called with a mid not present in unacked_publish")
        print("This is due to an unavoidable race-condition:")
        print("* publish() returns the mid of the message sent.")
        print("* mid from publish() is added to unacked_publish by the main thread")
        print("* on_publish() is called by the loop_start thread")
        print("While unlikely (because on_publish() will be called after a network round-trip),")
        print(" this is a race-condition that COULD happen")
        print("")
        print("The best solution to avoid race-condition is using the msg_info from publish()")
        print("We could also try using a list of acknowledged mid rather than removing from pending list,")
        print("but remember that mid could be re-used!")

def on_connect(client, userdata, flags, reason_code, properties):
    print(f"Connected with result code {reason_code}")
    client.subscribe(topic)

def on_message(client, userdata, msg):
    print(f"Received message '{msg.payload.decode()}' on topic '{msg.topic}'")

unacked_publish = set()

mqttc = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
mqttc.on_publish = on_publish
mqttc.on_connect = on_connect
mqttc.on_message = on_message
mqttc.user_data_set(unacked_publish)
mqttc.username_pw_set(username=USERNAME, password=PASSWORD)
mqttc.connect("mqtt.hextronics.cloud", 1883)
mqttc.loop_start()

msg_info = mqttc.publish(commands, "led.bl_off", 0, False)
unacked_publish.add(msg_info.mid)

while len(unacked_publish):
    time.sleep(0.1)

msg_info.wait_for_publish()
mqttc.disconnect()
mqttc.loop_stop()


## Parallel and Sequential Execution

To execute multiple commands at the same time or in sequence, use the `execute_in_parallel` and `execute_in_sequence` methods:

    ```python
from hextech import HexTechMusclev1

hex = HexTechMusclev1("COM5")

hex.execute_in_parallel([
    hex.blue_led.turn_on(False),
    hex.stepper0.move(2000, False)
])


## Programming with HexTech

### Controlling LEDs

The HexTech board has built-in LEDs that can be controlled using the `LED` class. Here are some basic commands:

* Turn on the blue LED:
  ```python
  hex.blue_led.turn_on()
  
* Turn off the blue LED:

    ```python
    hex.blue_led.turn_off()
    ```

* Flash the blue LED:

    ```python
    hex.blue_led.flash()
    ```
### Controlling Stepper Motors

The HexTech board can control up to four stepper motors. Here are some basic commands:

* Set speed:
    ```python
    hex.stepper0.set_speed(1000)
    ```

* Move the motor:
    ```python
    hex.stepper0.move(200)
    ```

## Controlling DC and BLDC Motors

The HexTech board can control one DC motor and one BLDC motor. Here are some basic commands:


