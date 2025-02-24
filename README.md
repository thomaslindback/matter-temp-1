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

> Matter shell is enabled. Will output the default. _NOT_ the codes if generte others below
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

> List env
Get-ChildItem env:

VendorId:        65521 (0xFFF1)
ProductId:       32769 (0x8001)
HardwareVersion: 0 (0x0)
PinCode:         20202021
Discriminator:   f00

https://github.com/espressif/esp-matter-tools/tree/main/mfg_tool#usage-examples
> change passcode and discriminator
esp-matter-mfg-tool -cn "My bulb" -v 0xFFF2 -p 0x8001 --pai `
    -k $Env:MATTER_SDK_PATH\credentials\test\attestation\Chip-Test-PAI-FFF2-8001-Key.pem `
    -c $Env:MATTER_SDK_PATH\credentials\test\attestation\Chip-Test-PAI-FFF2-8001-Cert.pem `
    -cd $Env:MATTER_SDK_PATH\credentials\test\certification-declaration/Chip-Test-CD-FFF2-8001.der `
    --passcode 20202022 --discriminator 3840  --enable-rotating-device-id  `
    --rd-id-uid d2f351f57bb9387445a5f92a601d1c14

WSL:

\\wsl$\Ubuntu\home\thoma

export MATTER_SDK_PATH=$ESP_MATTER_PATH/connectedhomeip/connectedhomeip

> Build chip-cert: https://developers.home.google.com/matter/test/certificates
>> make sure it is on the path: printenv 
export PATH=$PATH:$MATTER_SDK_PATH/src/credentials/out

esp-matter-mfg-tool -cn "My bulb" -v 0xFFF2 -p 0x8001 --pai \
    -k $MATTER_SDK_PATH/credentials/test/attestation/Chip-Test-PAI-FFF2-8001-Key.pem \
    -c $MATTER_SDK_PATH/credentials/test/attestation/Chip-Test-PAI-FFF2-8001-Cert.pem \
    -cd $MATTER_SDK_PATH/credentials/test/certification-declaration/Chip-Test-CD-FFF2-8001.der \
    --passcode 20202021 --discriminator 3840  --enable-rotating-device-id --rd-id-uid d2f351f57bb9387445a5f92a601d1c14

Flash from windows PS shell:
$Env:THE_BUILD="\\wsl$\Ubuntu\home\thoma\proj\matter-temp-1"

python -m esptool --chip esp32s3 -b 460800 --before default_reset --after hard_reset write_flash --flash_mode dio --flash_size 4MB --flash_freq 80m 0x0 $Env:THE_BUILD\build\bootloader\bootloader.bin 0xc000 $Env:THE_BUILD\build\partition_table\partition-table.bin 0x1d000 $Env:THE_BUILD\build\ota_data_initial.bin 0x20000 $Env:THE_BUILD\build\temp-1.bin


> In WSL merge bin
python -m esptool --chip ESP32-S3 merge_bin -o build/merged-flash.bin --flash_mode dio --flash_size 4MB 0x0 build/bootloader/bootloader.bin 0xc000 build/partition_table/partition-table.bin 0x1d000 build/ota_data_initial.bin 0x20000 build/temp-1.bin

> Wrote 0x190c20 bytes to file build/merged-flash.bin, ready to flash to offset 0x0


python -m esptool -p COM6 write_flash 0x10000 $Env:THE_BUILD\out\fff2_8001\f2d69df4-1099-49c0-babc-2cda5ab350ab\f2d69df4-1099-49c0-babc-2cda5ab350ab-partition.bin

$Env:PROJ_DIR="\\wsl$\Ubuntu\home\thoma\proj\matter-temp-1"

python -m esptool --chip esp32s3 -b 460800 --before default_reset --after hard_reset write_flash --flash_mode dio --flash_size 4MB --flash_freq 80m 0x0 $Env:PROJ_DIR\build\bootloader\bootloader.bin 0xc000 $Env:PROJ_DIR\build\partition_table\partition-table.bin 0x1d000 $Env:PROJ_DIR\build\ota_data_initial.bin 0x20000 $Env:PROJ_DIR\build\temp-1.bin

Flash only app:
python -m esptool --chip esp32s3 -b 460800 --before default_reset --after hard_reset write_flash --flash_mode dio --flash_size 4MB --flash_freq 80m 0x20000 $Env:PROJ_DIR\build\temp-1.bin

python -m esptool -p COM6 write_flash 0x10000 $Env:PROJ_DIR\out\fff2_8001\f2d69df4-1099-49c0-babc-2cda5ab350ab\f2d69df4-1099-49c0-babc-2cda5ab350ab-partition.bin
