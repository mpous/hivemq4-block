# HiveMQ MQTT broker balena block

This is a balena [Block](https://www.balena.io/blog/balenablocks-public-roadmap/) for interfacing with the [HiveMQ MQTT broker](https://www.hivemq.com/). 

HiveMQ is a popular MQTT broker that provides a scalable and reliable messaging platform for IoT applications. MQTT (Message Queuing Telemetry Transport) is a lightweight messaging protocol that is widely used in the IoT industry to facilitate communication between devices and applications. HiveMQ supports MQTT version 5.0, which includes many new features such as user properties, enhanced authentication, and control panel.

The main goal of this block is to reduce the friction of the configuration of HiveMQ in the edge. This block allows developers to change the configuration of the broker (`config.xml` and the `bridge extension` plus the `license`) using balena Device Variables instead of changing configuration files.

## Block configuration

To add the HiveMQ Block, add this service in your `docker-compose.yml`, as shown below.

```
  hivemq4:
    image: bh.cr/marc6/hivemq4-block
    ports:
      - 1883:1883   # MQTT/TCP protocol port
      - 8080:8080  # Control Center port
      - 8000:8000   # Websockets port
    volumes:
      - hivemq_data:/opt/hivemq/data                     # Make broker data persistent throughout stop/start cycles
      - hivemq_log:/opt/hivemq/log                       # Persist log data
      - hivemq_license:/opt/hivemq/license               # Persist HiveMQ license
    environment:
      JAVA_OPTS: "-XX:+UnlockExperimentalVMOptions -XX:InitialRAMPercentage=30 -XX:MaxRAMPercentage=80 -XX:MinRAMPercentage=30"

volumes:
  hivemq_data:
  hivemq_log:
  hivemq_license:
```

At this moment you will have a MQTT broker available on the port `1883`. You will be able to access the control panel from the port `8080`.

## Configure the HiveMQ MQTT broker

These variables you can set them in the balenaCloud `Device Variables` tab for the device (or globally for the whole application). None of them are mandatory and the MQTT broker in the edge might work without any variable defined.

Variable Name | Value | Description | Default
------------ | ------------- | ------------- | -------------
**`HIVEMQ_CONNECTION_ENABLED`** | `boolean` | Enables to change the bridge-extension.xml file with the `connection` tag. | `false`
**`HIVEMQ_CONNECTION_CONFIGURATION`** | `STRING (XML)` | Connection tag XML definition. | ```<connection><static><host>your-host-address</host><port>your-port</port></static></connection>```
**`HIVEMQ_AUTHENTICATION_ENABLED`** | `boolean` | Enables to change the bridge-extension.xml file to add the `authentication` tag. | `false`
**`HIVEMQ_AUTHENTICATION_CONFIGURATION`** | `STRING (XML)` | Authentication tag XML definition. | ```<authentication><mqtt-simple-authentication><username>your-username</username><password>your-password</password></mqtt-simple-authentication></authentication>```
**`HIVEMQ_TOPICS_CONFIGURATION`** | `STRING (XML)` | Topics tag XML definition on the bridge-extension.xml file. | ```<topics><topic><filter>plant1/compressors</filter></topic></topics>```
**`HIVEMQ_LICENSE`** | `STRING` | Your license file cntent in one unique line separated by `|`. Automatically the system will generate a `license.lic` file with the base64 content from this variable. | 
**`HIVEMQ_REST_API_ENABLED`** | `boolean` | Enables to change the config.xml file with the `rest-api` tag. | `false`
**`HIVEMQ_REST_API_CONFIGURATION`** | `STRING (XML)` | REST API tag XML definition on the config.xml file. | ```<rest-api><enabled>true</enabled><listeners><http><port>8888</port><bind-address>0.0.0.0</bind-address></http></listeners></rest-api>```


## Log in

The HiveMQ service exposes different ports, this is the default port and credentials to access them. Check the documentation for each of them to know how to change them using variables.

|Service|Port|Username|Password|
|:--|---|---|---|
|HiveMQ|8080 (http)|admin|hivemq|


## Attribution

- This is an adaptation of the [HiveMQ MQTT broker repository](https://github.com/hivemq/hivemq4-docker-images) where new variables have been added to work through Device Variables instead of configuration files.
- This is in part working thanks of the work of Kudzai Manditereza from HiveMQ and Marc Pous from balena.io.

## Troubleshooting

If you detect any issue using this block, feel free to contact us at the [forums.balena.io](https://forums.balena.io).



