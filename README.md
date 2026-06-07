# CC2-MQTT-API

Elegoo Centauri Carbon 2(ECC2) 3D Printer [ECC2_MQTT_API](./ECC2_MQTT_API.md)

For the latest MQTT API, it is recommended to refer to the [CC2_PROTOCOL.md](https://github.com/danielcherubini/elegoo-homeassistant/blob/main/docs/CC2_PROTOCOL.md).

# Bed Mesh Visualizer
Download the [Bed_Mesh_Visualizer.html](./Bed_Mesh_Visualizer.html) HTML file and open it with a browser.

After SSH remote login, download the `autosave.cfg` file from the `/opt/usr/cfg` directory, 
and then drag the autosave.cfg file into the browser that has the opened HTML page. 
You can then view the flatness of the net bed.

PS: The SSH password and Bed_Mesh_Visualizer.html is sourced from `Raelcun` on Discord `OpenCentauri`.

# Log zip decompression password

The zip archive of logs exported from the machine settings, Log zip decompression password: `16881688`.

# SSH Remote Login

Model: Elegoo Centauri Carbon 2

Current firmware version: V01.03.01.89

SSH_Address: Your printer's IP address, for example, `192.168.31.101`  

SSH_Port: `22`

SSH_Username(Account): `root`

SSH_Password: `MTY4ODE2`

# Firmware Update OTA Archive

The OTA upgrade package comes from the opencentauri community [firmware-update-archive](http://docs.opencentauri.cc/software/updates-cc2/#firmware-update-archive)


- stock firmware version: [02.00.02.00](https://github.com/suchmememanyskill/cc2-firmwares/raw/refs/heads/main/cc2-02.00.02.00-a3a76f4eb7036027e0927d7f9184a092-release-abroad.zip.sig)

MD5: `A3A76F4EB7036027E0927D7F9184A092`

v02.00.02.00 (Released 28/05/2026), This release removes the SSH process entirely.

This release prevents downgrades to any version before this release. If you'd like to downgrade anyway, use the [repacked version of v01.03.02.51](https://github.com/suchmememanyskill/cc2-firmwares/raw/refs/heads/main/cc2_eeb001_02.00.00.00_from51.zip.sig).


- Firmware with SSH added: [releases](https://github.com/xz0609/ECC2-MQTT-API/releases)

MD5: `C6546ADAC6C3DCB4F62A499F8873E5A4`

Based on the stock firmware v02.00.02.00 (Released 28/05/2026), add the ssh and sftp files back to the firmware.
To better distinguish files in the offline upgrade interface, the displayed version number has been specifically changed to 02.00.02.01

PS: Use at your own risk.
