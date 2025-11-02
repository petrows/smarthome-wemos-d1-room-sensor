# Simple room sensor, Tasmota-ready

This project designed to provide some simple-to-repeat, DIY sensor with features:

* Uses cheap Wemos-D1 board as CPU and WiFi
* Do not require SMD components installation, can be assembled with regural solder
* Simple construction, can be used as learning project
* Powered by USB from module (you can use variants with USB-C or Mini USB)
* Optional external power via terminal block
* Uses hi-precision climate sensor module
* Has three LED's, can bu used to display current CO2 level, as well as for any other purpose




## Tasmota config

To configure module, you have to use custom firmware, as Tasmota has ANTx sensors support [disabled by default](https://tasmota.github.io/docs/AHT2x/).

Simple workflow to upload proper firmware:

```bash
wget https://petro.ws/s/tasmota-15.1.0-pws-module-v2.bin -O tasmota.bin
esptool erase_flash
esptool write_flash -fm dout 0x0 tasmota.bin
```

Then configure Tasmota as usial, by connecting to built-in WiFI AP.

Next step is to apply template. Navigate to `Configuration` » `Other`. There in `Template` section paste template:
```json
{"NAME":"PWS-Room-v2","GPIO":[1,1,1,1,608,640,1,1,224,1632,225,1600,226,1],"FLAG":0,"BASE":18}
```
Enable `Activate` checkbox. Press `Save` to apply template.

Connect you sensors back and now you will see data from sensors read:

![Sensors data example](doc/tasmota-sensors.jpg "Sensors data example")

## Compile firmware

Prepare environment first.
Note: important to use same console, as it uses virtual environment activated.
All commands should be executed from repo root.

```bash
# Prepare Python env
python -m venv build/venv
source build/venv/bin/activate
# Install PlatformIO
pip install -U platformio
git clone https://github.com/arendst/Tasmota.git build/tasmota
```

Select release, what you want to use as a base:
```bash
cd build/tasmota
git checkout v15.1.0
```

Copy overrides config, where we have proper sensors enabled:
```bash
cp user_config_override.h build/tasmota/tasmota/
```

Now you can compile firmware. Replace upload port with your own:
```bash
cd build/tasmota
platformio run -e tasmota --target upload --upload-port /dev/ttyUSB0
```

Precompiled firmware can be found under `.pio/build/tasmota/firmware.bin`.

Precompiled firmwares could be found here:
* [Version 14.3.0](https://petro.ws/s/tasmota-14.3.0-pws-module-v2.bin)
* [Version 15.1.0](https://petro.ws/s/tasmota-15.1.0-pws-module-v2.bin)
