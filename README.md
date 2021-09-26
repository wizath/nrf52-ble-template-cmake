# nrf52-ble-template-cmake

Sample project using NRF5 SDK for creating sample ble_app_buttonless_dfu and secure_bootloader combo.
Project is configured in CMake for easy autocompletion in CLion.

Targets:
- dfu -- create dfu zip package for flashing
- full_hex -- creates app+settings+dfu bootloader+ softdevice hex combo for flashing via JLink
