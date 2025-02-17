# dbus-enphase-envoy - Emulates Enphase Microinverters as a single PV inverter

<small>GitHub repository: [mr-manuel/venus-os_dbus-enphase-envoy](https://github.com/mr-manuel/venus-os_dbus-enphase-envoy)</small>

## Index

1. [Disclaimer](#disclaimer)
1. [Supporting/Sponsoring this project](#supportingsponsoring-this-project)
1. [Purpose](#purpose)
1. [Requirement](#requirement)
1. [Config](#config)
1. [JSON structure](#json-structure)
1. [Install / Update](#install--update)
1. [Uninstall](#uninstall)
1. [Restart](#restart)
1. [Debugging](#debugging)
1. [Compatibility](#compatibility)
1. [Screenshots](#screenshots)

## Disclaimer

I wrote this script for myself. I'm not responsible, if you damage something using my script.


## Supporting/Sponsoring this project

You like the project and you want to support me?

[<img src="https://github.md0.eu/uploads/donate-button.svg" height="50">](https://www.paypal.com/donate/?hosted_button_id=3NEVZBDM5KABW)


### ⚠️ Update

Since `v0.2.0` this script works with the `D5.x.x` and `D7.x.x` firmware on the Enphase Envoy-S. You can choose the firmware in the `config.ini`.

The firmware `D7.x.x` has another authentication mechanism and needs a token for the local API access. This token is automatically requested and updated. To achieve this you need to enter your Enphase Enlighten credentials and the Envoy serial number in the `config.ini`.

### 💡 NOTE

Currently it works only for self-installers, since they are able to request a token that has the installer user. You can request a token over https://entrez.enphaseenergy.com/ -> Login -> For commissioned gateway -> Enter the name of your site (at least three letters to start the search), select the Gateway and then press create access token. Copy the token and paste it on https://www.jstoolset.com/jwt.

* If the `enphaseUser` is `installer` then this should work for you.
* If the `enphaseUser` is `owner` then this probably won't work for you.

I'm currently trying to find a solution with the Enphase Support for this.

UPDATE (2023.06.06): The needed API `/stream/meter` will be available with the `owner` token after the release of a firmware update at the end of June. See [Make "/stream/meter" available with "owner" token](https://support.enphase.com/s/question/0D53m00009CewmJCAR/make-streammeter-available-with-owner-token). After this happen I release the next stable release of this driver.

🚨 UPDATE (2023.09.18): Enphase did nothing until now. Your help is requested! Please also comment under [Make "/stream/meter" available with "owner" token](https://support.enphase.com/s/question/0D53m00009CewmJCAR/make-streammeter-available-with-owner-token). Additionally contact the Enphase support, by linking to the article above, tell them that you need this function and ask when it will be released.

## Purpose

This script adds the Enphase Microinverters as a single PV system in Venus OS. The data is fetched from the Enphase Envoy-S device and publishes on the dbus as the service `com.victronenergy.pvinverter.enphase_envoy` with the VRM instance `61`. The number of phases are automatically recognized, so it displays automatically the number of phases you are using (one, two or three).

It is also possible to publish the following data as JSON to a MQTT topic for other use (can be enabled in `config.ini`). For each list element a different topic is needed:

* **Meters** (PV, Grid, Consumption): Power, current, voltage, powerreact, powerappearent, powerfactor, frequency, whToday, vahToday, whLifetime, vahLifetime for total (except: powerfactor and frequency), L1, L2 and L3<br>
Note: If the power of the PV meter is below 5 W the driver will display 0 W. This prevents that a very small production power is shown when no sun is shining.

* **Devices** (Microinverters, Q-Relay): Serial number, device status, producing, communicating, provisioned and operating. For Q-Relay additionaly: relay [opened/closed] and reason

* **Inverters**: Last report date and last report watts

* **Events**: Latest 10 events from the Enphase Envoy-S


If you also want to use the Enphase Envoy-S as grid meter in Venus OS, then install the [mr-manuel/venus-os_dbus-mqtt-grid](https://github.com/mr-manuel/venus-os_dbus-mqtt-grid) package and insert the same MQTT broker and topic_meters in the `dbus-mqtt-grid/config.ini` as in `dbus-enphase-envoy/config.ini`. Don't forget to enable MQTT in the `dbus-enphase-envoy/config.ini`.

Shoudn't you already have a MQTT broker, than you can enable the Venus OS integrated MQTT broker under Venus OS GUI -> Menu -> Services -> MQTT on LAN (SSL) and if desired MQTT on LAN (Plaintext). In the `config.ini` insert the IP address of the Venus OS device or `127.0.0.1`.

## ⚠️ Requirement

The driver only works, if there is at least the production CT (Current Transformer) installed. Best would be, if you have a consumption and production CT installed. In nearly all cases it's possible to retrofit them. You can purchase the needed CT's as a pair in the [Enphase Store](https://enphase.com/store/communication/consumption-ct) or wherever you like. Ask your installer, if you don't know how to retrofit it by yourself.

![CT wiring diagram](./screenshots/ct-wiring-diagram.png)

## Config

Copy or rename the `config.sample.ini` to `config.ini` in the `dbus-enphase-envoy` folder and change it as you need it.


## JSON structure

<details><summary>PV, Grid, Consumption</summary>

```json
{
  "pv": {
    "L1": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "L2": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "L3": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "power": 0.000,
    "current": 0.000,
    "voltage": 0.000,
    "power_react": 0.00,
    "power_appearent": 0.000,
    "whToday": 0.000,
    "vahToday": 0.000,
    "whLifetime": 0.000,
    "vahLifetime": 0.000
  },
  "grid": {
    "L1": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "L2": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "L3": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "power": 0.000,
    "current": 0.000,
    "voltage": 0.000,
    "power_react": 0.00,
    "power_appearent": 0.000,
    "whToday": 0.000,
    "vahToday": 0.000,
    "whLifetime": 0.000,
    "vahLifetime": 0.000
  },
  "consumption": {
    "L1": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "L2": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "L3": {
      "power": 0.000,
      "current": 0.000,
      "voltage": 0.000,
      "power_react": 0.00,
      "power_appearent": 0.000,
      "power_factor": 0.00,
      "frequency": 0,
      "whToday": 0.000,
      "vahToday": 0.000,
      "whLifetime": 0.000,
      "vahLifetime": 0.000
    },
    "power": 0.000,
    "current": 0.000,
    "voltage": 0.000,
    "power_react": 0.00,
    "power_appearent": 0.000,
    "whToday": 0.000,
    "vahToday": 0.000,
    "whLifetime": 0.000,
    "vahLifetime": 0.000
  }
}
```
</details>

<details><summary>Devices</summary>

```json
{
  "inverters": {
    "122000000001": {
      "status": [
        "envoy.global.ok"
      ],
      "producing": true,
      "communicating": true,
      "provisioned": true,
      "operating": true
    },
    "122000000002": {
      "status": [
        "envoy.global.ok"
      ],
      "producing": true,
      "communicating": true,
      "provisioned": true,
      "operating": true
    },
    "122000000003": {
      "status": [
        "envoy.global.ok"
      ],
      "producing": true,
      "communicating": true,
      "provisioned": true,
      "operating": true
    }
  },
  "batteries": {},
  "relais": {
    "122100000001": {
      "status": [
        "envoy.global.ok"
      ],
      "producing": false,
      "communicating": true,
      "provisioned": true,
      "operating": true,
      "relay": "closed",
      "reason": "ok"
    }
  }
}
```
</details>

<details><summary>Inverters</summary>

```json
{
  "122000000001": {
    "lastReportDate": 1667471311,
    "lastReportWatts": 50
  },
  "122000000002": {
    "lastReportDate": 1667471311,
    "lastReportWatts": 60
  },
  "122000000003": {
    "lastReportDate": 1667471311,
    "lastReportWatts": 70
  }
}
```
</details>

<details><summary>Events</summary>

```json
{
  "3015": {
    "message": "Microinverter failed to report: Set",
    "serialNumber": "122000000001",
    "type": "pcu ",
    "datetime": "Wed Nov 02, 2022 04:53 PM CET"
  },
  "3016": {
    "message": "Microinverter failed to report: Clear",
    "serialNumber": "122000000002",
    "type": "pcu ",
    "datetime": "Wed Nov 02, 2022 04:54 PM CET"
  },
  "3017": {
    "message": "Microinverter failed to report: Set",
    "serialNumber": "122000000002",
    "type": "pcu ",
    "datetime": "Wed Nov 02, 2022 04:54 PM CET"
  },
  "3018": {
    "message": "Microinverter failed to report: Clear",
    "serialNumber": "122000000002",
    "type": "pcu ",
    "datetime": "Thu Nov 03, 2022 07:12 AM CET"
  },
  "3019": {
    "message": "Microinverter failed to report: Clear",
    "serialNumber": "122000000001",
    "type": "pcu ",
    "datetime": "Thu Nov 03, 2022 07:12 AM CET"
  },
  "3020": {
    "message": "Power On Reset",
    "serialNumber": "122000000002",
    "type": "pcu ",
    "datetime": "Thu Nov 03, 2022 07:12 AM CET"
  },
  "3021": {
    "message": "DC Voltage Too Low: Clear",
    "serialNumber": "122000000001",
    "type": "pcu channel 1",
    "datetime": "Thu Nov 03, 2022 07:42 AM CET"
  },
  "3022": {
    "message": "Power On Reset",
    "serialNumber": "122000000001",
    "type": "pcu ",
    "datetime": "Thu Nov 03, 2022 07:12 AM CET"
  },
  "3023": {
    "message": "DC Power Too Low: Clear",
    "serialNumber": "122000000002",
    "type": "pcu ",
    "datetime": "Thu Nov 03, 2022 08:01 AM CET"
  },
  "3024": {
    "message": "DC Power Too Low: Clear",
    "serialNumber": "122000000001",
    "type": "pcu ",
    "datetime": "Thu Nov 03, 2022 08:00 AM CET"
  }
}
```
</details>


## Install / Update

1. Login to your Venus OS device via SSH. See [Venus OS:Root Access](https://www.victronenergy.com/live/ccgx:root_access#root_access) for more details.

2. Execute this commands to download and copy the files:

    ```bash
    wget -O /tmp/download_dbus-enphase-envoy.sh https://raw.githubusercontent.com/mr-manuel/venus-os_dbus-enphase-envoy/master/download.sh

    bash /tmp/download_dbus-enphase-envoy.sh
    ```

3. Select the version you want to install.

4. Press enter for a single instance. For multiple instances, enter a number and press enter.

    Example:

    - Pressing enter or entering `1` will install the driver to `/data/etc/dbus-enphase-envoy`.
    - Entering `2` will install the driver to `/data/etc/dbus-enphase-envoy-2`.

### Extra steps for your first installation

5. Edit the config file to fit your needs. The correct command for your installation is shown after the installation.

    - If you pressed enter or entered `1` during installation:
    ```bash
    nano /data/etc/dbus-enphase-envoy/config.ini
    ```

    - If you entered `2` during installation:
    ```bash
    nano /data/etc/dbus-enphase-envoy-2/config.ini
    ```

6. Install the driver as a service. The correct command for your installation is shown after the installation.

    - If you pressed enter or entered `1` during installation:
    ```bash
    bash /data/etc/dbus-enphase-envoy/install.sh
    ```

    - If you entered `2` during installation:
    ```bash
    bash /data/etc/dbus-enphase-envoy-2/install.sh
    ```

    The daemon-tools should start this service automatically within seconds.

## Uninstall

⚠️ If you have multiple instances, ensure you choose the correct one. For example:

- To uninstall the default instance:
    ```bash
    bash /data/etc/dbus-enphase-envoy/uninstall.sh
    ```

- To uninstall the second instance:
    ```bash
    bash /data/etc/dbus-enphase-envoy-2/uninstall.sh
    ```

## Restart

⚠️ If you have multiple instances, ensure you choose the correct one. For example:

- To restart the default instance:
    ```bash
    bash /data/etc/dbus-enphase-envoy/restart.sh
    ```

- To restart the second instance:
    ```bash
    bash /data/etc/dbus-enphase-envoy-2/restart.sh
    ```

## Debugging

⚠️ If you have multiple instances, ensure you choose the correct one.

- To check the logs of the default instance:
    ```bash
    tail -n 100 -F /data/log/dbus-enphase-envoy/current | tai64nlocal
    ```

- To check the logs of the second instance:
    ```bash
    tail -n 100 -F /data/log/dbus-enphase-envoy-2/current | tai64nlocal
    ```

The service status can be checked with svstat `svstat /service/dbus-enphase-envoy`

This will output somethink like `/service/dbus-enphase-envoy: up (pid 5845) 185 seconds`

If the seconds are under 5 then the service crashes and gets restarted all the time. If you do not see anything in the logs you can increase the log level in `/data/etc/dbus-enphase-envoy/dbus-enphase-envoy.py` by changing `level=logging.WARNING` to `level=logging.INFO` or `level=logging.DEBUG`

If the script stops with the message `dbus.exceptions.NameExistsException: Bus name already exists: com.victronenergy.pvinverter.enphase_envoy"` it means that the service is still running or another service is using that bus name.

## Compatibility

This software supports the latest three stable versions of Venus OS. It may also work on older versions, but this is not guaranteed.

## Screenshots

<details><summary>Power L1</summary>

![Pv power L1 - pages](/screenshots/pv_power_L1_pages.png)
![Pv power L1 - device list](/screenshots/pv_power_L1_device-list.png)
![Pv power L1 - device list - enphase envoy 1](/screenshots/pv_power_L1_device-list_enphase-envoy-1.png)
![Pv power L1 - device list - enphase envoy 2](/screenshots/pv_power_L1_device-list_enphase-envoy-2.png)

</details>

<details><summary>Power L1 and L2</summary>

![Pv power L1, L2 - pages](/screenshots/pv_power_L2_L1_pages.png)
![Pv power L1, L2 - device list](/screenshots/pv_power_L2_L1_device-list.png)
![Pv power L1, L2 - device list - enphase envoy 1](/screenshots/pv_power_L2_L1_device-list_enphase-envoy-1.png)
![Pv power L1, L2 - device list - enphase envoy 2](/screenshots/pv_power_L2_L1_device-list_enphase-envoy-2.png)

</details>

<details><summary>Power L1, L2 and L3</summary>

![Pv power L1, L2, L3 - pages](/screenshots/pv_power_L3_L2_L1_pages.png)
![Pv power L1, L2, L3 - device list](/screenshots/pv_power_L3_L2_L1_device-list.png)
![Pv power L1, L2, L3 - device list - enphase envoy 1](/screenshots/pv_power_L3_L2_L1_device-list_enphase-envoy-1.png)
![Pv power L1, L2, L3 - device list - enphase envoy 2](/screenshots/pv_power_L3_L2_L1_device-list_enphase-envoy-2.png)

</details>
