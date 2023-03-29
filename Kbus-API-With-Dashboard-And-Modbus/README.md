# Node-Red-Examples
This application uses three main Docker images: Node-Red, Wago MQTT KBUS API, and Eclipse MQTT broker


# Docker Run commands
Node-Red Run command:
```
docker run --restart unless-stopped -d -p 1880:1880 --name node-red \
--security-opt seccomp:unconfined \
-v node_red_user_data:/data nodered/node-red
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

# Install Node Red Palettes
For this application, we'll need the modbus and Dashboard palettes. These can be installed via the Node Red palette manager 
![image](https://user-images.githubusercontent.com/42245728/226686627-73aff31a-a25d-4663-b1c4-686e9c256f0c.png)

![image](https://user-images.githubusercontent.com/42245728/226686501-8a24dfff-23f7-4c2b-8100-afb46c708491.png)
![image](https://user-images.githubusercontent.com/42245728/226686872-5d286c7d-c322-43c6-bc90-b09187f58393.png)


# Import Node-Red Code

![image](https://user-images.githubusercontent.com/42245728/226682888-9f534224-bc77-4359-bba4-53f67d8dd31c.png)

![image](https://user-images.githubusercontent.com/42245728/226684109-c682bddb-5bb1-4d97-ac86-2b1565a04b7b.png)

Import the .JSON Program from the following link:
https://github.com/jabdelmalak/Node-Red-Examples/blob/main/Kbus-API-With-Dashboard-And-Modbus/Node-red-Kbus-API-with-Dashboard-And-Modbus.json

# Changes to the Program

The Node-Red program consists of several flows. 
