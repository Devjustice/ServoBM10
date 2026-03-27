AT+CFUN=1
AT+IPR=9600
AT&W
AT+CGDCONT=1,"IP","ltemobile.apn"
AT+COPS=0
AT+CREG?


Step 1: Check Current USB Devices
bash

lsusb | grep Spreadtrum

or
bash

lsusb | grep 1782

Step 2: Reload USB Serial Driver
bash

sudo modprobe -r usbserial
sudo modprobe usbserial vendor=0x1782 product=0x000b

Step 3: Check If Devices Returned
bash

ls /dev/ttyUSB*

Step 4: If Still No Devices, Unplug and Replug

    Unplug BM10 from USB

    Wait 5 seconds

    Plug BM10 back in

    Run:

bash

ls /dev/ttyUSB*

Step 5: Check dmesg for Errors
bash

sudo dmesg | tail -20

Step 6: Alternative - Try ACM Device

Sometimes the BM10 shows as ttyACM instead:
bash

ls /dev/ttyACM*

Step 7: Full Reset of USB
bash

sudo modprobe -r usbserial
sudo modprobe -r option
sudo modprobe usbserial vendor=0x1782 product=0x000b

Step 8: Check All Serial Devices
bash

ls -la /dev/tty*

Quick Recovery Commands (Copy & Paste)
bash

# Unload driver
sudo modprobe -r usbserial

# Reload with BM10 IDs
sudo modprobe usbserial vendor=0x1782 product=0x000b

# Check for devices
ls /dev/ttyUSB*

# Set permissions
sudo chmod 666 /dev/ttyUSB0

# Connect
screen /dev/ttyUSB0 9600

