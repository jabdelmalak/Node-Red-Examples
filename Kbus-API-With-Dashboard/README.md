# Node-Red-Examples
This application uses three main Docker images: Node-Red, Wago MQTT KBUS API, and Eclipse MQTT broker. It's also important to note that you must have the select runtime as NONE on the web-based management page of the controller for the API to run. Additionally, ensure the PFC switch is set to RUN.

![image](https://user-images.githubusercontent.com/42245728/231490758-bbcc9cff-5461-42f8-97fe-d65121de19a6.png)

# Docker Run commands
Node-Red Run command:
```
docker run --restart unless-stopped -d -p 1880:1880 --name node-red \
--security-opt seccomp:unconfined \
-v node_red_user_data:/data nodered/node-red
```
Wago MQTT KBUS API Run command:
```
docker run -d --init --restart unless-stopped --privileged --network=host -v kbusapidata:/etc/kbus-api \
-v /var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket \jessejamescox/pfc-kbus-api
```
Eclipse MQTT Broker Run command:
```
docker run -d --network=host --restart=unless-stopped -v /home/docker/volumes/mosquitto/config/mosquitto.conf  eclipse-mosquitto:1.5
```

Change KBUS API config settings:
```
nano /home/docker/volumes/kbusapidata/_data/kbus-api.cfg
```
I've kept the default Node_ID as PFC200. This will be important when referencing the Node-Red Kbus API Node. 

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


Install Dashboard Palette

![image](https://user-images.githubusercontent.com/42245728/226686872-5d286c7d-c322-43c6-bc90-b09187f58393.png)

Install Kbus Palette 

![image](https://user-images.githubusercontent.com/42245728/228616364-b4490df3-57f4-4f11-b335-93b333165702.png)



# Import Node-Red Code

![image](https://user-images.githubusercontent.com/42245728/226682888-9f534224-bc77-4359-bba4-53f67d8dd31c.png)

![image](https://user-images.githubusercontent.com/42245728/226684109-c682bddb-5bb1-4d97-ac86-2b1565a04b7b.png)

Import the .JSON Program from the following link:
https://github.com/jabdelmalak/Node-Red-Examples/blob/97f5f28fe2e82d54a23ecaffb6ff93e10b30baad/Kbus-API-With-Dashboard/Node-Red-Kbus-API%20with%20Dashboard.json

# Changes to the Program

The Node-Red program consists of several flows. 

## Flow 1
The first flow centers around receiving MQTT data from the API via the Wago PFC Kbus In node. When dragging the Kbus In Node to seperated I/O nodes (shown below) make sure to use the lower output of the PFC200 Node. The lower output sends the data JSON message to the I/O which is what read for state analysis. Additionally, we are using the dashboard node to publish the state of the digital inputs via a GUI.

![image](https://user-images.githubusercontent.com/42245728/231223982-5ae4265a-4458-4478-90a3-cb6c9d345c80.png)

Here we are simply reading data from the PFC KBUS API and creating a list of global variables to be able to read/write the I/O to other flows. This picture specifically goes over the configuration for the digital and analog inputs.
![image](https://user-images.githubusercontent.com/42245728/231223917-9a02a6bd-5047-4d38-b657-093bf7e0ddf9.png)

Here is an example of a function specifically used to set the reading of an analog input to a global variable.

![image](https://user-images.githubusercontent.com/42245728/231238160-8b5159e4-4431-4bef-a9ed-ac5b72d86367.png)

Here I've created a ```controls``` structure that is seperated by the type of signal ```analogIn``` and the physical device that influences the input ```Zone1Valve1```. This variable within the structure is then linked to the output of the analog input node via ```msg.payload```.

When attempting to control the output side. We set a timer to actuate control functions every 500ms and then publish the output action to the output nodes and link them to the Kbus out node. The function after the timer is the control function that would dictate the state of the output. 

![image](https://user-images.githubusercontent.com/42245728/231238897-d798ab9f-1b2f-44e5-93b4-5a495fe7cfe4.png)

The control function for the output is fairly straightforward with the output being toggled by the alarm or button state. 

![image](https://user-images.githubusercontent.com/42245728/231239237-278962cc-8688-4fd8-b7ef-2e156c3fe715.png)

The button state is configured in Flow 2.

## Flow 2

Flow 2 is predominately used for dashboard updating. In an attempt to not bog down the processor and provide unnecessary refreshes for the dashboard, the graphs, buttons, and key value pairs are all updated within a slower refresh rate of 1 second. 

![image](https://user-images.githubusercontent.com/42245728/231240009-a7ea5a49-0f33-4eee-8ba1-5546a43815da.png)

The left side shows the 1 second recurring timestamp to update the analog graph and digital output values by extracting global variable information from the previous flow. The function is shown below:

![image](https://user-images.githubusercontent.com/42245728/231240521-8a4307a3-d1ba-4b09-9813-ea04bfd16583.png)

The right side of the flow shows the button Node which acts as an additional method of controlling the output.

All of these flows together bring the following UI

## The UI

Using the following url schema, you can deply the program and now access the UI: ```<IP_Address>:1880/ui```

![image](https://user-images.githubusercontent.com/42245728/231241044-d28cb23b-2b47-44dc-850d-84348d9e7b5b.png)


