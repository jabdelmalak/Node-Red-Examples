# Node-Red-Examples
This application uses three main Docker images: Node-Red, Wago MQTT KBUS API, and Eclipse MQTT broker


# Docker Run commands
Node-Red Run command:
```
docker run -d  --restart unless-stopped -p --network=host  -v node_red_data:/data --name mynodered nodered/node-red
```
Wago MQTT KBUS API Run command:
```
docker run -d --init --restart unless-stopped --privileged --network=host -v kbusapidata:/etc/kbus-api -v /var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket \jessejamescox/pfc-kbus-api
```
Eclipse MQTT Broker Run command:
```
docker run -d --network=host --restart=unless-stopped  -v /home/docker/volumes/mosquitto/config/mosquitto.conf  eclipse-mosquitto:1.5
```

Change KBUS API config settings:
```
nano /home/docker/volumes/kbusapidata/_data/kbus-api.cfg
```

More details on the Kbus API configuration file as well as the data structure presented by the MQTT message can be found here:

https://hub.docker.com/r/jessejamescox/pfc-kbus-api

# Confirm Docker container health

We must confirm that all containers are running on the device using the following docker command:
```
docker ps -a 
```
![image](https://user-images.githubusercontent.com/42245728/226682256-49597daa-98d8-4891-8e51-a0c56f6a50d9.png)
 
Once we've confirmed that containers are healthy, we can import our node red code. Use any browser (Google Chrome recommended) to type the IP address of your controller followed by :1880 into the browser. 

Ex: 192.168.1.10:1880

# Import Node-Red Code

![image](https://user-images.githubusercontent.com/42245728/226682888-9f534224-bc77-4359-bba4-53f67d8dd31c.png)

![image](https://user-images.githubusercontent.com/42245728/226684109-c682bddb-5bb1-4d97-ac86-2b1565a04b7b.png)

