# Firmware Setup and Installation
The following directory contains example klipper configurations for various 3D printer setups as well as instructions for firmware compilation and installation

## Firmware Compilation
1. It is assumed that you already have klipper installed on a host (e.g. Raspberry Pi) before proceeding.  
1. Log on to the your klipper installed host and run the following commands: 
```
cd ~/klipper  
make menuconfig  
```
1. In the configurator interface, enable `extra low-level configuration options`, choose `Micro-controoler Architecture (STMicroelectronics STM32)`, 
`Processor model (STM32F446)`, `Bootloader offset (No Bootloader)`, `Clock Reference (12 Mhz crystal)`, `Communication interface (USB (on PA11/PA12))`  

1. Exit the interface by typing "Q", save when prompted, then run the following commands:
```
make clean  
make  
```
1. A firmware file called `klipper.bin` will now be generated and can be located in the directory `~/klipper/out`.

## Firmware install (make FLASH method)
This method can be used if your leviathan already has klipper firmware installed.  

1. Make sure your host and leviathan are properly connected and powered.
1. Check that your host can properly communicate with the leviathan by running `ls /dev/serial/by-id/`
1. You should see something like `usb-Klipper_stm32f446xx_360026000A50534E4E313020-if00`, this is the USB ID of your leviathan, copy it down.
1. If you do not see any USB ID, it might mean that your leviathan has no firmware or a corrupt firmware. In this case, use the [DFU mode method](#firmware-install-dfu-mode-method) to install the firmware instead. 
1. follow the steps in [Firmware Compilation](#firmware-compilation) to make sure the configurator is correctly configured. 
1. Run the following commands, which will automatically compile, and then upload the firmware to the MCU directly:
```
cd ~/klipper  
make clean  
sudo service klipper stop  
make flash FLASH_DEVICE=/dev/serial/by-id/<your USB ID>  
sudo service klipper start  
```
1. If the installation was successful you should see `File downloaded successfully` somewhere among the text that was spit out.

## Firmware install (DFU mode method)
This method can be used even if there is no firmware installed on your leviathan.  

1. Make sure your host and leviathan are properly connected and powered.
1. Enter DFU mode by pressing down the 'reset' and 'boot0' switch on the leviathan, release 'reset' first and then 'boot0' second.
1. Check that your host can identify the MCU in DFU mode by running `dfu-util --list`, note the ID in the form of `[xxxx:yyyy]`.
1. follow the steps in [Firmware Compilation](#firmware-compilation) to make sure the configurator is correctly configured. 
1. Run the following commands, which will automatically compile, and then upload the firmware to the MCU directly:
```
cd ~/klipper  
make clean  
make flash FLASH_DEVICE=xxxx:yyyy
```
1. If the upload was successful, you should be able to see the leviathan USB ID by running `ls /dev/serial/by-id/`  
    