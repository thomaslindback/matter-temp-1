# Matter Sensors

This example demonstrates the integration of temperature and humidity sensors (SHTC3)
and an occupancy sensor (PIR). 

This application creates the temperature sensor, humidity sensor, and occupancy sensor
on endpoint 1, 2, and 3 respectively.

See the [docs](https://docs.espressif.com/projects/esp-matter/en/latest/esp32/developing.html)
for more information about building and flashing the firmware.

## Connecting the sensors

- Connecting the SHTC3, temperature and humidity sensor

| ESP32-C3 Pin | SHTC3 Pin |
|--------------|-----------|
| GND          | GND       |
| 3V3          | VCC       |
| GPIO 4       | SDA       |
| GPIO 5       | SCL       |

- Connecting the PIR sensor

| ESP32-C3 Pin | PIR Pin |
|--------------|---------|
| GND          | GND     |
| 3V3          | VCC     |
| GPIO 7       | Output  |

**_NOTE:_**:
- Above mentioned wiring connection is configured by default in the example.
- Ensure that the GPIO pins used for the sensors are correctly configured through menuconfig.
- Modify the configuration parameters as needed for your specific hardware setup.

## Usage

- Commission the app using Matter controller and read the attributes.

Below, we are using chip-tool to commission and subscribe the sensor attributes.
- 
```
# Commission
chip-tool pairing ble-wifi 1 (SSID) (PASSPHRASE) 20202021 3840

# Start chip-tool in interactive mode
chip-tool interactive start

# Subscribe to attributes
> temperaturemeasurement subscribe measured-value 3 10 1 1
> relativehumiditymeasurement subscribe measured-value 3 10 1 2
> occupancysensing subscribe occupancy 3 10 1 3
```

# Multiple On Off plugin unit

Build on Windows

Setup esp-idf v5.2.3 as in:
https://docs.espressif.com/projects/esp-matter/en/latest/esp32/developing.html#esp-matter-setup

Run: .\install.ps1 esp32
And then .\export.ps1

Add esp-matter as dependency:
idf.py add-dependency "espressif/esp_matter^1.4.0"

Change paths in CMakeLists.txt (x2) to point back to esp-matter-main/examples/common/app_reset
and esp-matter-main/examples/common/utils

Commission as in:
https://docs.espressif.com/projects/esp-matter/en/latest/esp32/developing.html#commissioning

Matter shell is enabled.
Check the onboarding code: matter onboardingcodes none
QRCode:            MT:-24J0-Q000KA0648G00
QRCodeUrl:         https://project-chip.github.io/connectedhomeip/qrcode.html?data=MT%3A-24J0-Q000KA0648G00
ManualPairingCode: 34970112332


Components options --> Chip device layer --> Device Identification Options
* change product id from 0x8000 to 0x8001


python -m pip install esp-matter-mfg-tool

$Env:<variable-name> = "<new-value>"
$Env:ESP_MATTER_PATH="C:\esp\esp-matter-main"
$Env:MATTER_SDK_PATH="C:\esp\connectedhomeip-master"
$env:Path = $Env:path + ";$Env:MATTER_SDK_PATH\src\tools"

List env
Get-ChildItem env:

VendorId:        65521 (0xFFF1)
ProductId:       32769 (0x8001)
HardwareVersion: 0 (0x0)
PinCode:         20202021
Discriminator:   f00

esp-matter-mfg-tool -cn "My bulb" -v 0xFFF2 -p 0x8001 --pai `
    -k $Env:MATTER_SDK_PATH\credentials\test\attestation\Chip-Test-PAI-FFF2-8001-Key.pem `
    -c $Env:MATTER_SDK_PATH\credentials\test\attestation\Chip-Test-PAI-FFF2-8001-Cert.pem `
    -cd $Env:MATTER_SDK_PATH\credentials\test\certification-declaration/Chip-Test-CD-FFF2-8001.der `
    --passcode 20202022 --discriminator 3840  --enable-rotating-device-id  `
    --rd-id-uid d2f351f57bb9387445a5f92a601d1c14

