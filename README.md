# Web Bluetooth

### What is this?
This is a demo of the BLE Notify support on the Nano 33 BLE.
 The Nano is the peripheral with the alarm service and alarm characteristic.
 Then when you navigate to this static HTML page on your mobile device's Chrome browser, it acts as the Android central role. Waiting for notifications from the Nano.

![mobile page](screen-mobile.png)
 
### Quickstart
0. You need an Edge Impulse project (or [use ours](https://studio.edgeimpulse.com/public/699161/live))
1. From Edge Impulse: Deployment, choose the Arduino library for the build target.
2. Add the resulting (.zip) file as a library in the Arduino workspace:
```bash
 arduino-cli lib install --zip-path ~/path/to/download/ei-<PROJECT_NAME>.zip
```
3. Find the sketch (.ino) in the library path:
```bash
 ls ~/Arduino/Libraries/<PROJECT_NAME>/examples/nano_ble33_sense/nano_ble33_sense_microphone
```
4. Make your changes for BLE (or [use ours](https://github.com/patterns/certification/blob/28fe69ef87690d60916bce50de26ca7d3f8f3eb2/ace/eep564/proj/nano_ble33_sense_microphone.ino))
5. Include the ArduinoBLE library:
```bash
arduino-cli lib install ArduinoBLE
```
6. Flash the sketch to the Nano:
```bash
 cd ~/Arduino/libraries/<PROJECT_NAME>/examples/nano_ble33_sense && \
    arduino-cli compile --fqbn arduino:mbed_nano:nano33ble nano_ble33_sense_microphone
 cd ~/Arduino/libraries/<PROJECT_NAME>/examples/nano_ble33_sense && \
    arduino-cli upload -p /dev/ttyACM0 --fqbn arduino:mbed_nano:nano33ble nano_ble33_sense_microphone
```
7. Start the serial comm to monitor the inference scores:
```bash
 arduino-cli monitor -p /dev/ttyACM0 --fqbn arduino:mbed_nano:nano33ble
```
8. From your mobile device (supports Bluetooth), navigate to this page; it's just static HTML so you can run a local file server (or [use gh-pages](https://patterns.github.io/web-bluetooth/))


### Configuration
- In our sketch, we define a `THRESHOLD_ALARM` value of 0.6 for alarm scores to meet in order to publish an event. You may want to adjust this for your noise scenarios.
- As the comment suggested, we disable the `EIDSP_QUANTIZE_FILTERBANK` macro to save 10K of memory.
- Also in the comments, we created the `boards.local.txt` to pass the build flag. We had the mbed core used in EI flash_linux.sh steps (other homework), as well as mbed_nano core (for Arduino sketches). So we made two copies of the `boards.local.txt`:
```bash
cat boards.local.txt
nano33ble.build.extra_flags= -DEI_CLASSIFIER_ALLOCATION_STATIC
cp boards.local.txt ~/.arduino15/packages/arduino/hardware/mbed/3.3.0/
cp boards.local.txt ~/.arduino15/packages/arduino/hardware/mbed_nano/4.2.4/
```

### Caveats
- Inside the Arduino library (.zip), there are two microphone examples. The `_continuous` version does not work with the BLE modified sketch. So double-check which one you are using, if you encounter errors about adjusting the "slices" per window.
- When using the scanner in the nRF Connect app from Nordic Semiconductor, you may not see the Nano peripheral unless you know the MAC address. There must be API calls in the ArduinoBLE library to specify the name to advertise. I didn't understand this at first, and didn't know the MAC address so thought the Nano was not showing in the scan results.
- The notify subscription takes 14 * 2 seconds to initialize (so alarms in that period will not be received by central).

### Credits
Bluetooth Notifications demo
 by [Google](https://github.com/project-idx/templates/tree/main/gemini)

Nano Bluetooth tutorial
 by [Arduino](https://docs.arduino.cc/tutorials/nano-33-ble-sense/ble-device-to-device/)
