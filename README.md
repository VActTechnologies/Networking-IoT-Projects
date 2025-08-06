# **Networking and IoT Projects**
## 1. **Send Data to Cloud Services (ThingSpeak, AWS IoT)**

### ThingSpeak (HTTP POST Request)
```
sudo apt install python3-requests

```
```
import requests

THINGSPEAK_API_KEY = 'YOUR_API_KEY'
data = {'field1': 25}  # Example sensor data
requests.post(f'https://api.thingspeak.com/update?api_key={THINGSPEAK_API_KEY}', data=data)

```
## 2. **Host Simple Web Server (Flask/Node.js)**

### Flask (Python)
1. **Install Python & Flask**
```
sudo apt update
sudo apt install python3 python3-pip
pip3 install flask

```
2. **Create Flask App**
```
mkdir flask_web && cd flask_web
nano app.py

```
```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return 'Hello from Flask Web Server on Raspberry Pi/Ubuntu!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)


```
**Run the Server**
``` 
python3 app.py
```

## 3 MQTT Publisher/Subscriber with Sensors
1. Install Mosquitto MQTT Broker & Clients
```
sudo apt update
sudo apt install mosquitto mosquitto-clients
sudo systemctl enable mosquitto
sudo systemctl start mosquitto

```
This will run the **local MQTT broker** on `localhost:1883`.
**2. Install Python MQTT Library**
```
pip3 install paho-mqtt

```
```
pip3 install paho-mqtt==1.6.1 --force-reinstall

```
**3. Publisher Script (Simulating Sensor Data)**

Example: Publish **Temperature & Humidity sensor data**
```
import paho.mqtt.client as mqtt

broker_address = "localhost"
topic = "sensors/temperature"

def on_message(client, userdata, message):
    print(f"Received Message: {message.payload.decode()} °C")

client = mqtt.Client("SensorSubscriber")
client.on_message = on_message
client.connect(broker_address)

client.subscribe(topic)
client.loop_forever()

```
Subscriber Script (Display Received Data)
```
import paho.mqtt.client as mqtt

broker_address = "localhost"
topic = "sensors/temperature"

def on_message(client, userdata, message):
    print(f"Received Message: {message.payload.decode()} °C")

client = mqtt.Client("SensorSubscriber")
client.connect(broker_address)

client.subscribe(topic)
client.on_message = on_message

client.loop_forever()

```
 **5. Run Both Scripts**

- Terminal 1:
```
python3 mqtt_subscriber.py

```
-  Terminal 2:
```
python3 mqtt_publisher.py

```

# Device to Device bluetooth communication

First, we need to pair with a bluetooth device to send information. From Ubuntu, we can pair to a Bluetooth device from Bluetooth settings. Alternatively, we can also use CLI to do the same.

```
 bluetoothctl
[NEW] Controller 24:0A:64:D7:99:AC asus [default]
[NEW] Device 94:E9:79:BB:F8:3A DESKTOP-C4ECO3K
[NEW] Device 88:79:7E:7B:4C:87 athene
[NEW] Device 94:65:2D:8C:2E:10 OnePlus 5
[NEW] Device 98:0C:A5:61:D5:64 Lenovo VIBE K5 Plus
[NEW] Device AC:C3:3A:A0:CE:EF Galaxy J2
[NEW] Device 98:D3:35:71:02:B3 HC-05

[bluetooth]# power on
Changing power on succeeded

[bluetooth]# agent on
Agent registered

[bluetooth]# default-agent
Default agent request successful

[bluetooth]# scan on
Discovery started
[CHG] Controller 24:0A:64:D7:99:AC Discovering: yes
[CHG] Device 94:E9:79:BB:F8:3A RSSI: -88
[CHG] Device 88:79:7E:7B:4C:87 RSSI: -66

[bluetooth]# pair 88:79:7E:7B:4C:87
Attempting to pair with 88:79:7E:7B:4C:87
[CHG] Device 88:79:7E:7B:4C:87 Paired: yes
Pairing successful
```

To communicate with paired devices, we will use [RFCOMM protocol](https://en.wikipedia.org/wiki/List_of_Bluetooth_protocols). RFCOMM is just a serial port emulation and provides reliable data tranfer like TCP.

From ubuntu, lets open a port for communication.

```
$ sudo rfcomm listen /dev/rfcomm0 3
```

From Android, we have to connect to ubuntu. For this, we can use [Roboremo](https://play.google.com/store/apps/details?id=com.hardcodedjoy.roboremofree&hl=en) app which supports RFCOMM.

```
$ sudo rfcomm listen /dev/rfcomm0 3
Waiting for connection on channel 3
Connection from 88:79:7E:7B:4C:87 to /dev/rfcomm0
Press CTRL-C for hangup 
```


Once the connection is established, we can communicate between devices.

In Unix like systems, OS provides a device file as an interface for device driver. To send and read messages from Linux or Mac is as easy as reading and writing to a file.

# to send message to bluetooth
``` $ echo 'hello from ubuntu' > /dev/rfcomm0 ```


We can see the received messages on Android
We can also send messages from android and read from ubuntu.

# to read messages from bluetooth
```
$ cat /dev/rfcomm0
hello from android
```


This way, we can communicate with any bluetooth module using a laptop or a smart phone.

# WIFI GUI using QT 

## refer this repo [wifi-hotspot](https://github.com/lakinduakash/linux-wifi-hotspot)

```
sudo add-apt-repository ppa:lakinduakash/lwh
sudo apt update
sudo apt install linux-wifi-hotspot
```
