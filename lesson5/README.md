# Lesson 5: Crossbar.io and Paho

* [Publish-subscribe](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)
* [Web application messaging protocol](https://en.wikipedia.org/wiki/Web_Application_Messaging_Protocol) (WAMP)
* [cURL](https://en.wikipedia.org/wiki/CURL)
* [Docker](https://en.wikipedia.org/wiki/Docker_(software))
* [Crossbar.io](https://crossbar.io/docs/Getting-Started/)
* [Message queuing telemetry transport](https://en.wikipedia.org/wiki/MQTT) (MQTT)
* [Eclipse Foundation](https://en.wikipedia.org/wiki/Eclipse_Foundation)
* [Eclipse Mosquitto](https://mosquitto.org/)
* [Eclipse Paho](https://en.wikipedia.org/wiki/Eclipse_Paho)

## Lab 5A: Crossbar.io

### Install Docker on a Raspberry Pi
* [Docker Get Started](https://www.docker.com/get-started) includes Docker Desktop download for Mac or Windows and Docker Engine for Linux (Fedora, CentOS, AWS, Azure, Ubuntu, Debian)
```sh
$ curl -sSL get.docker.com | sh
```
### Add pi to the Docker Group as a non-root user (Control-d to logout and reconnect via SSH for this to take effect)
```sh
$ sudo usermod -aG docker pi
$ logout
```
### Run Docker images designed to work on ARM under the prefix armhf
```sh
$ docker run -it armhf/alpine /bin/sh
/ # cat /etc/os-release
/ # echo "Hi, this is a tiny Linux distribution!" | base64
/ # echo "SGksIHRoaXMgaXMgYSB0aW55IExpbnV4IGRpc3RyaWJ1dGlvbiEK" | base64 -d
/ # exit
$ docker run armhf/alpine date
```
### Build and run new image from Dockerfile
```sh
$ docker images
$ cd ~/demo
$ cp ~/iot/lesson5/Dockerfile .
$ docker build -t curl_docker .
$ docker run curl_docker
$ docker images
$ docker run -it balenalib/rpi-raspbian /bin/sh
# cat /etc/os-release
# pwd
# ls
# exit
```

### Run Crossbar.io router on Terminal 1
```sh
$ git clone https://github.com/crossbario/crossbar-examples
$ cd crossbar-examples/getting-started
$ docker pull crossbario/crossbar-armhf
$ docker run -v $PWD:/node -u 0 --rm --name=crossbar -it -p 8080:8080 crossbario/crossbar-armhf
```
### Run publish-client on Terminal 2
```sh
$ sudo pip3 install -U autobahn[twisted,encryption,serialization,xbr]
$ cd crossbar-examples/getting-started/1.hello-world/
$ python3 client_component_publish.py
```
### Run subscribe-client on Terminal 3
```sh
$ cd crossbar-examples/getting-started/1.hello-world/
$ python3 client_component_subscribe.py
```
## Lab 5B: Eclipse Mosquitto and Eclipse Paho

### Install and run Mosquitto and Paho to subscribe on one terminal and publish on another
```sh
$ sudo apt install mosquitto mosquitto-clients
$ mosquitto_sub -h localhost -v -t "\$SYS/#"
```
### Press control-c to stop mosquitto_sub
```sh
$ mosquitto_sub -h localhost -v -t test/topic &
```
### Publish "Hello" on another terminal
```sh
$ mosquitto_pub -h localhost -t test/topic -m "Hello"
$ service mosquitto status
$ netstat -tln
$ sudo pip3 install -U paho-mqtt
$ git clone https://github.com/eclipse/paho.mqtt.python.git
$ cd ~/iot/lesson5
$ python3 client.py
```
#### Terminal 1 (control-c to exit)
```sh
$ python3 sub.py
```
#### Terminal 2 on the same or another Raspberry Pi
```sh
$ python3 pub.py
```
#### Terminal 1 (control-c to exit)
```sh
$ python3 sub-multiple.py
```
#### Terminal 2 on the same or another Raspberry Pi
```sh
$ python3 pub-multiple.py
```
#### Terminal 1 (control-c to exit)
```sh
python3 subraspi.py
```
#### Terminal 2 on the same or another Raspberry Pi
```sh
python3 pubraspi.py
```
