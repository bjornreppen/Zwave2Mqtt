# Zwave To MQTT

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![MIT Licence](https://badges.frapsoft.com/os/mit/mit.png?v=103)](https://opensource.org/licenses/mit-license.php)
[![Pulls](https://img.shields.io/docker/pulls/robertslando/zwave2mqtt.svg)](https://hub.docker.com/r/robertslando/zwave2mqtt)
[![Build](https://img.shields.io/docker/cloud/build/robertslando/zwave2mqtt.svg)](https://hub.docker.com/r/robertslando/zwave2mqtt)
[![Image size](https://images.microbadger.com/badges/image/robertslando/zwave2mqtt.svg)](https://hub.docker.com/r/robertslando/zwave2mqtt "Get your own image badge on microbadger.com")

<a href="https://www.buymeacoffee.com/MVg9wc2HE" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important;box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" ></a>

[![Join channel](https://img.shields.io/badge/SLACK-zwave2mqtt.slack.com-red.svg?style=popout&logo=slack&logoColor=red)](https://join.slack.com/t/zwave2mqtt/shared_invite/enQtNjc4NjgyNjc3NDI2LTc3OGQzYmJlZDIzZTJhMzUzZWQ3M2Q3NThmMjY5MGY1MTc4NjFiOWZhZWE5YjNmNGE0OWRjZjJiMjliZGQyYmU "Join channel")

![OpenZWave](docs/OZW_Logo.png)
**TO**
![MQTT](docs/MQTT-Logo.png)

Fully configurable Zwave to MQTT **Gateway** and **Control Panel**.

- **Backend**: NodeJS, Express, socket.io, Mqttjs, openzwave-shared, Webpack
- **Frontend**: Vue,  socket.io, [Vuetify](https://github.com/vuetifyjs/vuetify)

## 📖 Table of contents

- [Zwave To MQTT](#zwave-to-mqtt)
  - [📖 Table of contents](#%f0%9f%93%96-table-of-contents)
  - [:electric_plug: Installation](#electricplug-installation)
    - [DOCKER :tada: way](#docker-tada-way)
    - [NodeJS or PKG version](#nodejs-or-pkg-version)
  - [:nerd_face: Development](#nerdface-development)
  - [:wrench: Usage](#wrench-usage)
    - [Zwave](#zwave)
    - [MQTT](#mqtt)
    - [Gateway](#gateway)
      - [Special topics](#special-topics)
      - [Gateway values table](#gateway-values-table)
  - [:file_folder: Nodes Management](#filefolder-nodes-management)
    - [Add a node](#add-a-node)
    - [Remove a node](#remove-a-node)
    - [Replace failed node](#replace-failed-node)
  - [:star: Features](#star-features)
  - [:robot: Home Assistant integration (BETA)](#robot-home-assistant-integration-beta)
    - [Components management](#components-management)
      - [Edit existing component](#edit-existing-component)
      - [Add new component](#add-new-component)
    - [Custom Components](#custom-components)
      - [Thermostats](#thermostats)
      - [Fans](#fans)
  - [:gift: MQTT APIs](#gift-mqtt-apis)
    - [Zwave APIs](#zwave-apis)
    - [Set values](#set-values)
    - [Broadcast](#broadcast)
  - [:camera: Screenshots](#camera-screenshots)
    - [Settings](#settings)
    - [Control Panel](#control-panel)
    - [Groups associations](#groups-associations)
    - [Scenes](#scenes)
    - [Debug](#debug)
  - [:question: FAQ](#question-faq)
  - [:pray: Thanks](#pray-thanks)
  - [:pencil: TODOs](#pencil-todos)
  - [:bowtie: Author](#bowtie-author)

## :electric_plug: Installation

### DOCKER :tada: way

Check [docker repo](https://github.com/robertsLando/Zwave2Mqtt-docker#install) for more info

```bash
# Using volumes as persistence
docker run --rm -it -p 8091:8091 --device=/dev/ttyACM0 --mount source=zwave2mqtt,target=/usr/src/app/store robertslando/zwave2mqtt:latest

# Using local folder as persistence
mkdir store
docker run --rm -it -p 8091:8091 --device=/dev/ttyACM0 -v $(pwd)/store:/usr/src/app/store robertslando/zwave2mqtt:latest

# As a service
wget https://raw.githubusercontent.com/robertsLando/Zwave2Mqtt-docker/master/compose/docker-compose.yml
docker-compose up
```

> Replace `/dev/ttyACM0` with your serial device

Enjoy :smile:

### NodeJS or PKG version

1. Firstly you need to install [Open-Zwave](https://github.com/OpenZWave/open-zwave) library on your system.

    If you are using Ubuntu:

    ```sh
    sudo apt-get install libudev-dev
    cd ~
    git clone -b 1.4 https://github.com/OpenZWave/open-zwave.git
    cd openzwave && make && sudo make install
    sudo ln -s /usr/local/lib64/libopenzwave.so /usr/local/lib/libopenzwave.so
    sudo ln -s /usr/local/lib64/libopenzwave.so.1.4 /usr/local/lib/libopenzwave.so.1.4
    sudo ldconfig
    export LD_LIBRARY_PATH=/usr/local/lib64
    sudo sed -i '$a LD_LIBRARY_PATH=/usr/local/lib64' /etc/environment
    ```

    For Raspberry check [here](https://github.com/OpenZWave/node-openzwave-shared/blob/master/README-raspbian.md#2-install-the-open-zwave-library-on-your-raspberry)

2. Test the library: go to openzwave directory `cd openzwave-*` and run the command

    `MinOZW /dev/ttyACM0`

    > replace `/dev/ttyACM0` with the USB port where your controller is connected

3. Now you can use the packaged version (you don't need NodeJS/npm installed) or clone this repo and build the project:

    - For the packaged version:

       ```sh
       cd ~
       mkdir Zwave2Mqtt
       cd Zwave2Mqtt
       wget https://github.com/OpenZWave/Zwave2Mqtt/releases/download/1.0.0/zwave2mqtt-v1.0.0.zip
       unzip zwave2mqtt-v1.0.0_PKG.zip
       ./zwave2mqtt
       ```

    - If you want to compile last code from github:

      ```sh
      git clone https://github.com/OpenZWave/Zwave2Mqtt
      cd Zwave2Mqtt
      npm install
      npm run build
      npm start
      ```

4. Open the browser <http://localhost:8091>

## :nerd_face: Development

Developers who wants to debug the application have to open 2 terminals.

In first terminal run `npm run dev` to start webpack-dev for front-end developing and hot reloading at <http://localhost:8092>
(**THE PORT FOR DEVELOPING IS 8092**)

In the second terminal run `npm run dev:server` to start the backend server with inspect and auto restart features (if you don't have nodemon installed: `npm install -g nodemon`)

To package the application run `npm run pkg` command and follow the steps

## :wrench: Usage

Firstly you need to open the browser at the link <http://localhost:8091> and edit the settings for Zwave, MQTT and the Gateway.

### Zwave

Zwave settings:

- **Serial port**: The serial port where your controller is connected
- **Network key** (Optional): Zwave network key if security is enabled. The correct format is `"0xCA,0xFE,0xBA,0xBE,.... "` (16 bytes total)
- **Logging**: Enable/Disable Openzwave Library logging
- **Save configuration**: Store zwave configuration in `zwcfg_<homeHex>.xml` and `zwscene.xml` files this is needed for persistent node information like node name and location
- **Poll interval**: Interval in milliseconds between polls (should not be less than 1s per device)
- **Configuration Path**: The path to Openzwave devices config db
- **Assume Awake**: Assume Devices that support the Wakeup Class are awake when starting up OZW

### MQTT

Mqtt settings:

- **Name**: A unique name that identify the Gateway.
- **Host**: The url of the broker. Insert here the protocol if present, example: `tls://localhost`. Mqtt supports this protocols:  `mqtt`, `mqtts`, `tcp`, `tls`, `ws`, `wss`, `wss` and `mqtts`
- **Port**: Broker port
- **Reconnect period**: Milliseconds between two reconnection tries
- **Prefix**: The prefix where all values are published
- **QoS**: Quality Of Service (check MQTT specs) of outgoing packets
- **Retain**: The retain flag of outgoing packets
- **Clean**: Sets the clean flag when connecting to the broker
- **Store**: Enable/Disable persistent storage of packets (QoS > 0). If disabled in memory storage will be used but all packets stored in memory are lost in case of shutdowns or unexpected errors.
- **Allow self signed certs**: When using encrypted protocols, set this to true to allow self signed certificates (**WARNING** this could expose you to man in the middle attacks)
- **Ca Cert and Key**: Certificate Authority, Client Key and Client Certificate files required for secured connections (if broker requires valid certificates, this fields can be leave empty otherwise)
- **Auth**: Enable this if broker requires auth. If so you need to enter also a valid **username** and **password**.

### Gateway

Gateway settings:

- **Gateway type**: This setting specify the logic used to publish Zwave Nodes Values in MQTT topics. At the moment there are 3 possible configuration, two are automatic (all values are published in a specific topic) and one needs to manually configure which values you want to publish to MQTT and what topic to use. For every gateway type you can set custom topic values, if gateway is not in 'configure manually' mode you can omit the topic of the values (the topic will depends on the gateway type) and use the table to set values you want to `poll` or if you want to scale them using `post operation`

  1. **ValueId Topics**: *Automatically configured*. The topic where zwave values are published will be:

      `<mqtt_prefix>/<?node_location>/<node_id>/<class_id>/<instance>/<index>`

      - `mqtt_prefix`: the prefix set in Mqtt Settings
      - `node_location`: location of the Zwave Node (optional, if not present will not be added to the topic)
      - `node_id`: the unique numerical id of the node in Zwave network
      - `class_id`: the numerical class id of the value
      - `instance`: the numerical value of value instance
      - `index`: the numerical index of the value

  2. **Named Topics**: *Automatically configured*. **DEPRECATED** After a discussion with Openzwave author lib we discourage users to use this configuration as we cannot ensure that value labels will be the same, they could change in future versions (and also they depends on localization added in OZW 1.6). You can find more info [HERE](https://github.com/OpenZWave/Zwave2Mqtt/issues/22)
  
      The topic where zwave values are published will be:

      `<mqtt_prefix>/<?node_location>/<node_name>/<class_name>/<?instance>/<value_label>`

      - `mqtt_prefix`: the prefix set in Mqtt Settings
      - `node_location`: location of the Zwave Node (optional, if not present will not be added to the topic)
      - `node_name`: name of the node, if not set will be `nodeID_<node_id>`
      - `class_name`: the node class name corresponding to given class id or `unknownClass_<class_id>` if the class name is not found
      - `?instance`: Used just with multi-instance devices. The main instance (1) will not have this part in the topic but other instances will have: `instance_<instance_index>`
      - `value_label`: the zwave value label (lower case and spaces are replaced with `_`)

  3. **Configured Manually**: *Needs configuration*. The topic where zwave values are published will be:

      `<mqtt_prefix>/<?node_location>/<node_name>/<value_topic>`

      - `mqtt_prefix`: the prefix set in Mqtt Settings
      - `node_location`: location of the Zwave Node (optional, if not present will not be added to the topic)
      - `node_name`: name of the node, if not set will be `nodeID_<node_id>`
      - `value_topic`: the topic you want to use for that value (take from gateway values table).

- **Payload type**: The content of the payload when an update is published:
  - **JSON Time-Value**: The payload will be a JSON object like:

    ```json
    {
      "time": 1548683523859,
      "value": 10
    }
    ```

  - **Entire Zwave value Object**
  The payload will contain all info of a value from Zwave network:

      ```json
      {
        "value_id": "3-64-1-0",
        "node_id": 3,
        "class_id": 64,
        "type": "list",
        "genre": "user",
        "instance": 1,
        "index": 0,
        "label": "Mode",
        "units": "",
        "help": "",
        "read_only": false,
        "write_only": false,
        "min": 0,
        "max": 0,
        "is_polled": false,
        "values": ["Off", "Heat (Default)", "Cool", "Energy Heat"],
        "value": "Off",
      }
      ```

  - **Just value**: The payload will contain only the row Numeric/String value

- **Ignore status updates**: Enable this to prevent gateway to send an MQTT message when a node changes its status (dead/sleep == false, alive == true)
- **Ignore location**: Enable this to remove nodes location from topics
- **Send 'list' as integer**: Zwave 'list' values are sent as list index instead of string values
- **Use nodes name instead of numeric nodeIDs**: When gateway type is `ValueId` use this flag to force to use node names instead of node ids in topic.
- :star:**Hass discovery**:star:: Enable this to automatically create entities on Hass using MQTT autodiscovery (more about this [here](#star-Home-Assistant-integration-BETA))
- **Discovery Prefix**: The prefix to use to send MQTT discovery messages to HASS

Once finished press `SAVE` and gateway will start Zwave Network Scan, than go to 'Control Panel' section and wait until the scan is completed to check discovered devices and manage them.

Settings, scenes and Zwave configuration are stored in `JSON/xml` files under project `store` folder that you can easily **import/export** for backup purposes.

#### Special topics

- **Node status** (`true` if node is ready `false` otherwise) will be published in:

`<mqtt_prefix>/<?node_location>/<node_name>/status`

- **Node events** (value will be the event code) will be published in:

`<mqtt_prefix>/<?node_location>/<node_name>/event`

- **Scene events** (value will be the scene event code) will be published in:

`<mqtt_prefix>/<?node_location>/<node_name>/scene/event`

#### Gateway values table

The Gateway values table can be used with all gateway types to customize specific values topic for each device type found in the network and do some operations with them. Each value has this properties:

- **Device**: The device type. Once scan is complete, the gateway creates an array with all devices types found in the network. A device has a `device_id` that is unique, it is composed by this node properties: `<manufacturerid>-<productid>-<producttype>`.
- **Value**: The value you want to customize
- **Device Class**: If the value is a multilevel sensor, a binary sensor or a meter you can set a custom `device_class` to use with home assistant discovery. Check [sensor](https://www.home-assistant.io/components/sensor/#device-class) and [binary sensor](https://www.home-assistant.io/components/binary_sensor/#device-class)
- **Topic**: The topic to use for this value. It is the topic added  after topic prefix, node name and location. If gateway type is different than `Manual` this can be leave blank and the value topic will be the one based on the gateway configuration chosen
- **Post operation**: If you want to convert your value (eg. '/10' '/100' '*10' '*100')
- **Poll**: Enable this to set the value `enablePoll` flag
- **Verify Changes**: Used to verify changes of this values

## :file_folder: Nodes Management

### Add a node

To add a node using the UI select the controller Action `Add Node (inclusion)`, click send (:airplane:) button to enable the inclusion mode in your controller and enable the inclusion mode in your device to. `Controller status` will be `waiting` when inclusion has been successfully enabled on the controller and `completed` when the node has been successfully added. Wait few seconds and your node will be visible in the table once ready.

### Remove a node

To add a node using the UI select the controller Action `Remove Node (exclusion)`, click send (:airplane:) button to enable the exclusion mode in your controller and enable the exclusion mode in your device to. `Controller status` will be `waiting` when exclusion has been successfully enabled on the controller and `completed` when the node has been successfully removed. Wait few seconds and your node will be removed from the table.

### Replace failed node

To replace a failed node from the UI you have to use the command `Replace Failed Node`, if everything is ok the controller will start inclusion mode and status will be `Waiting`, now enable inclusion on your device to add it to the network by replacing the failed one.

## :star: Features

- Configurable Zwave to Mqtt Gateway
- Home Assistant integration (**beta**)
- Zwave Control Panel:
  - **Nodes management**: check all nodes discovered in the z-wave network, send/receive nodes values updates directly from the UI and send action to the nodes and controller for diagnostics and network heal
  - **Custom Node naming and Location**: Starting from v1.3.0 nodes `name` and `location` are stored in a JSON file named `nodes.json`. This because not all nodes have native support for naming and location features ([#45](https://github.com/OpenZWave/Zwave2Mqtt/issues/45)). This change is back compatible with older versions of this package: on startup it will get all nodes names and location from the `zwcfg_homeHEX.xml` file (if present) and create the new `nodes.json` file based on that. This file can be imported/exported from the UI control panel with the import/export buttons placed on the top of nodes table, on the right of controller actions select.
  - **Groups associations**: create associations between nodes (also supports multi-instance associations, need to use last version of openzwave-shared)
  - **Custom scenes management**: (OpenZwave-Shared scenes management has actually some bugs and it's limited so I have made a custom scenes implementation that uses the same APIs but stores values in a JSON file that can be imported/exported and also allows to set a timeout to a value in a scene)
- Log debug in UI

## :robot: Home Assistant integration (BETA)

**At least Home Assistant >= 0.84 is required!**

The easiest way to integrate Zwave2Mqtt with Home Assistant is by
using [MQTT discovery](https://www.home-assistant.io/docs/mqtt/discovery/).
This allows Zwave2Mqtt to automatically add devices to Home Assistant.
To enable this feature remember to set the flag **Hass Discovery** in Gateway settings configuration.

To achieve the best possible integration (including MQTT discovery):

- In your **Zwave2Mqtt** gateway settings enable `Homeassistant discovery` flag and enable the MQTT **retain** too. The retain flag for MQTT is suggested to be sure that, once discovered, each device get the last value published (otherwise you have to wait for a value change)
- In your **Home Assistant** `configuration.yaml`:

```yaml
mqtt:
  discovery: true
  discovery_prefix: <your_discovery_prefix>
  broker: [YOUR MQTT BROKER]  # Remove if you want to use builtin-in MQTT broker
  birth_message:
    topic: 'hass/status'
    payload: 'online'
  will_message:
    topic: 'hass/status'
    payload: 'offline'
```

Mind you that if you want to use the embedded broker of Home Assistant you
have to [follow this guide](https://www.home-assistant.io/docs/mqtt/broker#embedded-broker).

Zwave2Mqtt is expecting Home Assistant to send it's birth/will
messages to `hass/status`. Be sure to add this to your `configuration.yaml` if you want
Zwave2Mqtt to resend the cached values when Home Assistant restarts.

Zwave2Mqtt try to do its best to guess how to map devices from Zwave to HASS. At the moment it try to guess the device to generate based on zwave values command classes, index and units of the value. When the discovered device doesn't fit your needs you can you can set custom a `device_class` to values using Gateway value table.

### Components management

To see the components that have been discovered by Zwave2Mqtt go to Control Panel UI, select a Node from the Nodes table then select the Node tab from tabs menu at the bottom of Nodes table. Now at the Bottom of the page, after Node values section you can find a new section called `Home Assistant - Devices`. Here you will see a table with all devices created for the selected node.

![Hass Devices](docs/hass_devices.png)

**ATTENTION**
Once edited the devices will loose all their customizations after a restart. To prevent this you can store the node hassDevices by pressing `STORE` button at the top of hass devices table. By pressing it the hassDevices will be stored in `nodes.json` file that can be imported/exported easily from control panel UI at the top of nodes table.

#### Edit existing component

If you select a device it's configuration will be displayed as a JSON object on the right. With the selected device you can edit it and send some actions:

- `Update`: Update in-memory hass device configuration
- `Rediscover`: Re-discover this device using the `discoveryTopic` and `discovery_payload` of the configuration
- `Delete`: Delete the device from Hass entities of selected node

#### Add new component

If no device is selected you can manually insert a device JSON configuration. If the configuration is valid you can press the button `Add` to add it to devices. If the process complete successfully the device will be added to the Hass Devices table and you can now select it from the table and press on `Rediscover` to discover your custom device

### Custom Components

At the moment auto discovery just creates components like `sensor`, `cover` `binary_sensor` and `switch`. For more complex components like `climate` and `fan` you need to provide a configuration. Components configurations are stored in `hass/devices.js` file. Here are contained all components that Zwave2MQTT needs to create for each Zwave device type. The key is the Zwave device unique id (`<manufacturerid>-<productid>-<producttype>`) the value is an array with all HASS components to create for that Zwave Device.

#### Thermostats

```js
{ // Heatit Thermostat TF 021 (ThermoFloor AS)
    type: 'climate',
    object_id: 'thermostat',
    values: ['64-1-0', '49-1-1', '67-1-1', '67-1-2'],
    mode_map: {'off': 'Off', 'heat': 'Heat (Default)', 'cool': 'Cool'},
    setpoint_topic: { "Heat (Default)": '67-1-1', "Cool": '67-1-2' },
    default_setpoint: '67-1-1',
    discovery_payload: {
      min_temp: 15,
      max_temp: 30,
      modes: ['off', 'heat', 'cool'],
      mode_state_topic: '64-1-0',
      mode_command_topic: true,
      current_temperature_topic: '49-1-1',
      current_temperature_template: '{{ value_json.value }}',
      temperature_state_template: '{{ value_json.value }}',
      temperature_command_topic: true
    }
  }
```

- **type**: The hass [MQTT component](https://www.home-assistant.io/components/mqtt/) type
- **object_id**: The unique id of this object (must be unique for the device)
- **values**: Array of values used by this component
- **mode_map**: Key-Value object where keys are [MQTT Climate](https://www.home-assistant.io/components/climate.mqtt/) modes and values are the matching thermostat modes values
- **setpoint_topic**: Key-Value object where keys are the modes of the Zwave thermostat and values are the matching setpoint `value_id` (use this if your thermostat has more than one setpoint)
- **default_setpoint**: The default thermostat setpoint.
- **discovery_payload**: The payload sent to hass to discover this device
  - **min_temp/max_temp**: Min/Max temperature of the thermostat
  - **modes**: Array of Hass Climate supported modes. Allowed values are `[“auto”, “off”, “cool”, “heat”, “dry”, “fan_only”]`
  - **mode_state_topic**: `value_id` of mode value
  - **current_temperature_topic**: `value_id` of current temperature value
  - **current_temperature_template/temperature_state_template**: Template used to fetch the value from the MQTT payload
  - **temperature_command_topic/mode_command_topic**: If true this values are subscribed to this topics to send commands from Hass to change this values

Thermostats are most complex components to create, in this device example the setpoint topic changes based on the mode selected. Zwave2Mqtt handles the mode changes by updating the device discovery payload to match the correct setpoint based on the mode selected.

#### Fans

```js
{ // GE 1724 Dimmer
    type: 'fan',
    object_id: 'dimmer',
    values: ['38-1-0'],
    discovery_payload: {
      command_topic: "38-1-0",
      speed_command_topic: "38-1-0",
      speed_state_topic: "38-1-0",
      state_topic: "38-1-0",
      speeds: ["off", "low", "medium", "high"],
      payload_low_speed: 24,
      payload_medium_speed: 50,
      payload_high_speed: 99,
      payload_off: 0,
      payload_on: 99,
      state_value_template: "{% if (value_json.value | int) == 0 %} 0 {% else %} 99 {% endif %}",
      speed_value_template: "{% if (value_json.value | int) == 25 %}  24  {% elif (value_json.value | int) == 51 %} 50 {% elif (value_json.value | int) == 99 %} 99 {% else %}  0  {% endif %}"
    }
}
```

- **type**: The hass [MQTT component](https://www.home-assistant.io/components/mqtt/) type
- **object_id**: The unique id of this object (must be unique for the device)
- **values**: Array of values used by this component
- **discovery_payload**: The payload sent to hass to discover this device
  - **command_topic**: The topic to send commands
  - **state_topic**: The topic to receive state updates
  - **speed_command_topic**: The topic used to send speed commands
  - **state_value_template**: The template used to set the value ON/OFF based on the payload received
  - **speed_value_template**: The template to use to set the speed `["off", "low", "medium", "high"]` based on the payload received

## :gift: MQTT APIs

You have full access to all [Openzwave-Shared APIs](https://github.com/OpenZWave/node-openzwave-shared/blob/master/README-api.md) (and more) by simply using MQTT.

### Zwave APIs

To call a Zwave API you just need to publish a JSON object like:

```json
{
  "args": [2,1]
}
```

Where `args` is an array with the args used to call the api, the topic is:

`<mqtt_prefix>/_CLIENTS/ZWAVE_GATEWAY-<mqtt_name>/api/<api_name>/set`

The result will be published on the same topic without `/set`

Example: If I publish the previous json object to the topic

`zwave/_CLIENTS/ZWAVE_GATEWAY-office/api/getAssociations/set`

I will get this response (in the same topic without the suffix `/set`):

```json
{
  "success":true,
  "message":"Success zwave api call",
  "result":[1]
}
```

`result` will contain the value returned from the API. In this example I will get an array with all node IDs that are associated to the group 1 (lifeline) of node 2.

### Set values

To write a value using MQTT you just need to send the value to set in the same topic where the value updates are published by adding the suffix `/set` to the topic (**READONLY VALUES CANNOT BE WRITE**).

Example with gateway configured with `named topics`:

If I publish the value `25.5` (also a payload with a JSON object with the value in `value` property is accepted) to the topic

`zwave/office/nodeID_4/thermostat_setpoint/heating/set`

I will set the Heating setpoint of the node with id `4` located in the `office` to `25.5`. To check if the value has been successfully write just check when the value changes on the topic:

`zwave/office/nodeID_4/thermostat_setpoint/heating`

### Broadcast

You can send broadcast values to *all values with a specific suffix* in the network.

Broadcast API is accessible from:

`<mqtt_prefix>/_CLIENTS/ZWAVE_GATEWAY-<mqtt_name>/broadcast/<value_topic_suffix>/set`

- `value_topic_suffix`: the suffix of the topic of the value I want to control using broadcast.

It works like the set value API without the node name and location properties.
If the API is correctly called the same payload of the request will be published
to the topic without `/set` suffix.

Example of broadcast command (gateway configured as `named topics`):

`zwave/_CLIENTS/ZWAVE_GATEWAY-test/broadcast/thermostat_setpoint/heating/set`

Payload: `25.5`

All nodes with command class `thermostat_setpoint` and value `heating` will be set to `25.5` and I will get the same value on the topic:

`zwave/_CLIENTS/ZWAVE_GATEWAY-test/broadcast/thermostat_setpoint/heating`

## :camera: Screenshots

### Settings

![OpenZWave](docs/settings.png)

### Control Panel

![Control Panel](docs/OZW_Panel_Node.png)

### Groups associations

![Groups](docs/groups_associations.png)

### Scenes

![Scenes](docs/scenes.png)

### Debug

![Debug](docs/debug.png)

## :question: FAQ

> A: Why when I add a value to Gateway values table I don't see all my devices?

**B: When adding values to the gateway values table it shows JUST ONE DEVICE FOR EACH TYPE. This is to make it easier and faster to setup your network as if you have a network with lot devices (light, light dimmers for example) you just need to add the values you want to bridge to mqtt (for a light it will always be just the switch to turn it on/off for exmple without all configuration values) and it will bridge those values for all the devices of that type (without configure the values one by one).**

> A: My device is X and has been discovered as Y, why?

**B: Hass Discovery is not easy, zwave have many different devices with different values. To try to understand how to discover a specific value I have used** [this](https://github.com/OpenZWave/open-zwave/blob/master/config/Localization.xml) **file that shows what kind of value is expeted based on value class and index. Unfortunally not all devices respect this specifications so for those cases I have created Hass Devices table where you can manually fix the discovery payload and than save it to make it persistent. I have also created a file** `/hass/devices.js` **where I place all devices specific values configuration, your contribution is needed there, so submit a PR with your files specification to help it grow.**

## :pray: Thanks

Thanks to this people for help with issues tracking and contributions:

- @mlankamp for mesh graph feature and typescript support
- @sjorge for testing with different os and Openzwave 1.6
- @jshridha for testing home assistant integration and contribution

## :pencil: TODOs

- [x] Better logging
- [x] Dockerize application
- [x] Package application with PKG
- [x] HASS integration, check [zigbee2mqtt](https://github.com/Koenkk/zigbee2mqtt/blob/master/lib/extension/homeassistant.js)
- [ ] Add unit test
- [ ] JSON validator for settings and scenes
- [ ] Better nodes status management using 'testNode'
- [ ] Network graph to show neighbours using [vue-d3-network](https://github.com/emiliorizzo/vue-d3-network)

## :bowtie: Author

[Daniel Lando](https://github.com/robertsLando)

Support me on [Patreon](https://www.patreon.com/user?u=16906849) :heart:
