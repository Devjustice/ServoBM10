# ServoBM10


Servo BM10 Rogers Setup Guide - Quick Reference
Step 1: Connect USB and Find Device
bash

# After plugging in, bind the USB serial driver
sudo modprobe usbserial vendor=0x1782 product=0x000b

The device will appear as /dev/ttyUSB0, /dev/ttyUSB1, /dev/ttyUSB2, /dev/ttyUSB3
Step 2: Find AT Command Port

The AT command port is usually /dev/ttyUSB0 or /dev/ttyUSB2. Test with:
bash

screen /dev/ttyUSB0 115200

Type AT - if you see OK, you're on the right port.
Step 3: Set Baud Rate (Rogers Standard: 9600)

Check supported rates first:
text

AT+IPR=?

Set to 9600 (Rogers standard):
text

AT+IPR=9600
AT&W

Exit screen (Ctrl+A, then K), then reconnect:
bash

screen /dev/ttyUSB0 9600

Step 4: Configure Rogers APN
text

AT+CGDCONT=1,"IP","lteinternet.apn"

Step 5: Check SIM and Signal
text

AT+CPIN?          # Should show READY
AT+CSQ            # Signal strength (0-31)
AT+CREG?          # Registration status (0,1 or 0,5 = connected)

Step 6: Register on Network
text

AT+COPS=0         # Auto-select network

Or manually select Rogers:
text

AT+COPS=1,2,"302720"

Step 7: Verify Connection
text

AT+CREG?          # Should show +CREG: 0,1 or +CREG: 0,5
AT+CGPADDR=1      # Should show IP address

Important Notes
Supported Baud Rates (from actual device):

    1200, 2400, 4800, 9600, 19200, 38400, 57600, 115200, 230400, 460800

    2700 is NOT supported - use 9600 for Rogers

Device Detection:
bash

# If device doesn't appear as ttyUSB*
sudo modprobe usbserial vendor=0x1782 product=0x000b

Permissions:
bash

sudo chmod 666 /dev/ttyUSB*

Network Registration Status Codes:

    +CREG: 0,1 = Registered to home network

    +CREG: 0,5 = Registered (roaming)

    +CREG: 2,8 = No network/searching

Common Rogers APNs:

    lteinternet.apn - Standard LTE data

    internet.com - Legacy data

    m2m.com.rogers.com - M2M/IoT devices

Exit screen:

Press Ctrl+A then K, then Y to confirm
Full Connection Script
bash

#!/bin/bash
# Rogers BM10 Connection Script

PORT="/dev/ttyUSB0"
BAUD=9600

# Bind driver if needed
sudo modprobe usbserial vendor=0x1782 product=0x000b 2>/dev/null

# Set permissions
sudo chmod 666 $PORT

# Connect
screen $PORT $BAUD

Happy connecting!
