# Shelly smart home platform for HASS and HASSIO

[![founder-wip](https://img.shields.io/badge/founder-Håkan_Åkerberg@StyraHem.se-green.svg?style=for-the-badge)](https://www.styrahem.se)
[![buy me a coffee](https://img.shields.io/badge/If%20you%20like%20it-Buy%20me%20a%20coffee-orange.svg?style=for-the-badge)](https://www.buymeacoffee.com/styrahem)

![stability-wip](https://img.shields.io/badge/stability-stable-green.svg?style=for-the-badge)
![version-wip](https://img.shields.io/badge/latest_version-0.1.0-green.svg?style=for-the-badge)
[![hacs_badge](https://img.shields.io/badge/HACS-Default-green.svg?style=for-the-badge)](https://github.com/custom-components/hacs)

This platform adds components for Shelly smart home devices to Home Assistant. There is no configuration needed, it will find all devices on your LAN and add them to Home Assistant. All communication with Shelly devices is local. You can use this plugin and continue to use Shelly Cloud, MQTT and Shelly app in your mobile if you want. A proxy can also be used to include Shellies on different LAN's.

![List](https://raw.githubusercontent.com/StyraHem/ShellyForHASS/master/images/intro.png)

## Features

- Automatically discover all Shelly devices
- Monitor status (state, temperature, humidity, power, rssi, ip, fw, battery, uptime etc.)
- Control (turn on/off, dim, color, effects, up/down etc.)
- Sensors for most of the attributes
- Switch sensors to show status of switch button (0.0.16)
- Detection of click and multiple quick clicks by events
- Works with Shelly default settings, no extra configuration
- Runs locally, you don't have to add the device to Shelly Cloud
- Coexists with Shelly Cloud so you can continue to use Shelly Cloud and Shelly apps
- Using CoAP and REST for communication (not MQTT)
- Working with both static or dynamic ip addresses on your devices
- Using events so very fast response (no polling)
- Support restrict login with username and password (0.0.3-)
- Version sensor to show version of component and pyShelly (0.0.4)
- Device configuration (name, show switch as light) (0.0.4)
- Discovery can be turned off (0.0.4)
- Switch for firmware update trigger (use with monster-card to show a list of devices to need to be update, see examples below)
- Support proxy to allow Shelly devices in other LANs (0.0.15).

## Devices supported

- Shelly 1
- Shelly 1 PM (no power meter, firmware bug)
- Shelly 2 (relay or roller mode)
- Shelly 2.5 (relay or roller mode)
- Shelly 4
- Shelly Bulb
- Shelly EM
- Shelly H&T
- Shelly Plug
- Shelly Plug S
- Shelly RGBWW
- Shelly RGBW2
- Shelly 2LED (not verified)
- Shelly Flood
- Shelly Dimmer
- Shelly EM (comming soon)

## Installation

### Install with HACS  (recomended)

Do you you have [HACS](https://community.home-assistant.io/t/custom-component-hacs) installed? Just search for Shelly and install it direct from HACS. HACS will keep track of updates and you can easly upgrade Shelly to latest version.

### Install with Custom Updater (deprecated)
_We recomend you use HACS as Customer Updater is deprecated._

Do you you have [Custom updater](https://github.com/custom-components/custom_updater) installed? Then you can use the service [custom_updater.install](https://github.com/custom-components/custom_updater/wiki/Services#install-element-cardcomponentpython_script) with the parameter {"element":"shelly"} to install Shelly.

Custom updater also let you to upgrade to latest version. We recomend you to use this.

### Install manually

1. Install this platform by creating a `custom_components` folder in the same folder as your configuration.yaml, if it doesn't already exist.
2. Create another folder `shelly` in the `custom_components` folder. Copy all 5 Python (.py) files from custom_components into the `shelly` folder. Use `raw version` if you copy and paste the files from the browser.

## Configure

When you have installed shelly and make sure it exists under `custom_components` folder it is time to configure it in Home Assistant.

It is very easy, just add `shelly:` to your `configuration.yaml`

### Examples

#### Default with discovery and power sensors

```yaml
shelly:
```

#### Without discovery - manually specify devices

```yaml
shelly:
  discovery: false
  version: true #add version sensor
  sensors:
    - all
  devices:      #devices to be added
    - id: "420FC7"
    - id: "13498B-1"   #Shelly 2, Id + Channel number
    - id: "7BD5F3"
      name: My cool plug   #set friendly name
      sensors: #overide global (all)
         - power
         - device_temp
```

#### With discovery - adjust some devices

```yaml
shelly:
  discovery: true  #add all devices (default)
  sensors: #sensors to show, can be override on each device
    - rssi
    - uptime
  devices:  #configure devices
    - id: "420FC7"
      light_switch: true  #add this switch as a light
      sensors: [ switch ] #Override the global sensor
    - id: "7BD5F3"
      name: My cool plug #set friendly name
```

#### Sensor, global and per device

```yaml
shelly:
  discovery: true  #add all devices (default)
  sensors:
    - all #show all sensors
  devices:  #configure devices
    - id: "420FC7"
      light_switch: true  #add this switch as a light
    - id: "7BD5F3"
      name: My cool plug #set friendly name
```

#### Events

```yaml
automation:
  - alias: "Shelly turn off and then on quickly, any switch"
    trigger:
      platform: event
      event_type: shelly_switch_click
      event_data:
        click_cnt: 2
        state: True
  - alias: "Shelly double click (momentary) on a specific switch"
    trigger:
      platform: event
      event_type: shelly_switch_click
      event_data:
        entity_id: sensor.shelly_shsw_1_XXXXXX_switch
        click_cnt: 4        
```

### Parameters

| Parameter              | Description                                                                                            | Default | Version |
|------------------------|--------------------------------------------------------------------------------------------------------|---------|---------|
| username               | User name to use for restrict login                                                                    |         | 0.0.3-  |
| password               | Password to use for restrict login                                                                     |         | 0.0.3-  |
| discovery              | Enable or disable discovery                                                                            | True    | 0.0.4-  |
| version                | Add a version sensor to with version of component and pyShelly                                         | False   | 0.0.4-  |
| devices                | Config for each device, se next table for more info                                                    |         | 0.0.4-  |
| show_id_in_name        | Add Shelly Device id to the end of the name                                                            | False   | 0.0.5-  |
| id_prefix              | Change the prefix of the entity id and unique id of the device                                         | shelly  | 0.0.5-  |
| igmp_fix               | Enable sending out IP_ADD_MEMBERSHIP every minute                                                      | False   | 0.0.5-  |
| additional_information | Retrieve additional information (rssi, ssid, uptime, ..)                                               | True    | 0.0.6-  |
| scan_interval          | Update frequency for additional information                                                            | 60      | 0.0.6-  |
| _wifi_sensor_          | Add extra sensor for wifi signal of each device. Requires `additional_information` to be `True`.  | False   | 0.0.6 (deprecated) |
| _uptime_sensor_        | Add extra sensor for device uptime of each devivce. Requires `additional_information` to be `True` | False   | 0.0.6 (deprecated)  |
| power_decimals         | Round power sensor values to the given number of decimals                                          |         | 0.0.14- |
| sensors                | A list with sensors to show for each device. See list below.                                        | power | 0.0.15- |
| upgrade_switch         | Add firmware switches when upgrade needed.                                                           | True  | 0.0.15- |
| unavailable_after_sec  | Number of seconds before the device will be unavialable      | 60 | 0.0.16- |

#### Device configuration

| Parameter    | Description                                                                               | Example        | Version |
|--------------|-------------------------------------------------------------------------------------------|----------------|---------|
| id           | Device id, same as in mobile app                                                          | 421FC7         |         |
| name         | Specify if you want to set a name of the device                                           | My Cool Shelly |         |
| entity_id    | Override the auto generated part of entity_id, like shsw_1_500500                         | bedlamp        | 0.0.16- |
| light_switch | Show this switch as a light                                                               | True           |         |
| sensors      | A list with sensors to show for each device. This will override the global sensors. See list below.  |                | 0.0.15- |
| upgrade_switch | Add firmware switches when upgrade needed. Override global configuration.               | False    | 0.0.15- |
| unavailable_after_sec  | Overide number of seconds before the device will be unavialable.    | 120 | 0.0.16- | 

#### Sensors
| Sensor       | Description                           | Values / Unit     |
|--------------|---------------------------------------|-------------------|
| all          | Show all available sensors            |                   |
| power        | Show power consumtion sensors         | W                 |
| rssi         | Show WiFi quality sensors             | dB                |
| uptime       | Show uptime sensors                   | s                 |
| over_power   | Show over power sensors               | True, False       |
| device_temp  | Show device inner temperature sensors | °C                |
| over_temp    | Show over temperature sensors         | True, False       |
| cloud        | Show cloud status                     | disabled, disconnected, connected |
| mqtt         | Show mqtt connection state            | True, False       |
| battery      | Show battery percentage (H&T)         | %                 |
| switch       | Show state of the actual switch button| True, False       |

All of the sensors (not power) require additional_information to be True to work.

If you disable discovery only Shellies under devices will be added.

You can only specify one username and password for restrict login. If you enter username and password, access to devices without restrict login will continue to work. Different logins to different devices will be added later.

### Events 
[Events added in release 0.0.16]
#### shelly_switch_click
When the switch sensor is enabled an event will be sent for multiple clicks on the switch button. This can be used to trig automations for double clicks etc.

```json
{
    "event_type": "shelly_switch_click",
    "data": {
        "entity_id": "sensor.shelly_shsw_1_xxxxxx_switch",
        "click_cnt": 4,
        "state": true
}
```
| Parameter | Description                                                                                 |
|-----------|---------------------------------------------------------------------------------------------|
| click_cnt | Number of clicks, 2 = turn back and forth quickly, 4 = double click on momentary switch.    |
| state     | Current state of the switch, can be uset to distinct on-off-on from off-on-off for example. |

### Restart Home Assistant

Now you should restart Home Assistant to load shelly. Some times you need to restart twice to get the required library pyShelly installed. You can see this error in the log file.

Shelly will discover all devices on your LAN and show them as light, switch, sensor and cover in Home Assistant.

### Proxy for VLAN or different network
If you running Shellies on different VLAN or network there is a [proxy.py](https://github.com/StyraHem/ShellyForHASS/blob/master/util/proxy.py) that can be used to forward CoAP messages to ShellyForHASS plugin.

Update the script with the ip-address of your HASS installation and run it on a computer/router etc that are connected to same nettwork as your Shellies. Firewall and routing must be enabled, TCP 80 HASS -> Shelly and UDP 5683 Shelly -> HASS.

## Monster card
You can use the component with [monstercard](https://github.com/ciotlosm/custom-lovelace/tree/master/monster-card) to present data in a nice way.

### All shelly devices
```yaml
card:
  show_header_toggle: false
  title: Shelly
  type: entities
filter:
  exclude:
    - entity_id: '*rssi*'
    - entity_id: '*uptime*'
    - entity_id: '*firmware*'
  include:
    - entity_id: '*shelly*'
type: 'custom:monster-card'
```

### Need firmware update (click on the switch to update)
```yaml
card:
  show_header_toggle: false
  title: Shelly need update
  type: entities
filter:
  include:
    - entity_id: '*firmware_update*'
type: 'custom:monster-card'
```

### WiFi signal of all devices (rssi)
```yaml
card:
  show_header_toggle: false
  title: Shelly
  type: entities
filter:
  include:
    - entity_id: '*rssi*'
type: 'custom:monster-card'
```

## Feedback

Please give us feedback on info@styrahem.se or Facebook groups: [Shelly grupp (Swedish)](https://www.facebook.com/groups/ShellySweden) or [Shelly support group (English)](https://www.facebook.com/groups/ShellyIoTCommunitySupport/)

## Founder

This plugin is created by the StyraHem.se, the Swedish distributor of Shelly. In Sweden you can buy Shellies from [StyraHem.se](https://www.styrahem.se/c/126/shelly) or any of the retailers like m.nu, Kjell&Company etc.

[![buy me a coffee](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/styrahem)

## Thanks to
- [@tefinger](https://github.com/tefinger) that have test and add functinallity to improve this component.
- Allterco that have developed all nice Shellies and also response quickly on requests and bugfixes.

## Screen shots

![List](https://raw.githubusercontent.com/StyraHem/ShellyForHASS/master/screenshots/List.PNG)
![RGB](https://raw.githubusercontent.com/StyraHem/ShellyForHASS/master/screenshots/RGB.PNG)
![Effects](https://raw.githubusercontent.com/StyraHem/ShellyForHASS/master/screenshots/Effects.PNG)
![White](https://raw.githubusercontent.com/StyraHem/ShellyForHASS/master/screenshots/White.PNG)
