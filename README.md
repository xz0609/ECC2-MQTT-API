# CC2-MQTT-API

Elegoo Centauri Carbon 2(ECC2) 3D Printer MQTT API

[ECC2_MQTT_API 文档](./ECC2_MQTT_API.md)

For the latest MQTT API, it is recommended to refer to the [CC2_PROTOCOL.md]](https://github.com/danielcherubini/elegoo-homeassistant/blob/main/docs/CC2_PROTOCOL.md).

# SSH Remote Login

Model: Elegoo Centauri Carbon 2

Current firmware version: V01.03.01.89

SSH address: Your printer's IP address, for example, `192.168.31.101`

SSH port: `22`

SSH account: `root`

SSH password: `MTY40DE2`

# Bed Mesh Visualizer
Download the [Bed_Mesh_Visualizer.html](./Bed_Mesh_Visualizer.html) HTML file and open it with a browser.

After SSH remote login, download the `autosave.cfg` file from the `/opt/usr/cfg` directory, 
and then drag the autosave.cfg file into the browser that has the opened HTML page. 
You can then view the flatness of the net bed.

PS: The SSH password and Bed_Mesh_Visualizer.html is sourced from `Raelcun` on Discord `OpenCentauri`.
