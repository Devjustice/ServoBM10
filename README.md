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



Now I see the issue clearly. Your BM10 has no signal:

    +CREG: 2,0 = Not registered, no signal

    +CSQ: 0,99 = Zero signal strength

    +CGATT: 0 = Not attached to data network

The Problem

Your BM10 is not picking up any cellular signal. This is why it won't register or make calls.
Most Likely Causes
1. No Antenna Connected

The BM10 requires an external antenna to get signal. Check your device:

    Look for a small metal connector labeled ANT, MAIN, or RF

    Is there an antenna attached?

    If not, you need to connect one

2. Weak Signal Area

Even with antenna, you may be in a low-coverage area
3. Hardware Issue

The modem's radio may be disabled or faulty
Step 1: Check if Antenna is Connected

Important: Does your BM10 have an antenna connected?

    Look at the device - there should be a small antenna connector

    If no antenna, you won't get any signal

    Even a small wire can help for testing

Step 2: Enable Full Radio Power
text

AT+CFUN=1


Great! Your APN is now set correctly to ltemobile.apn. Now let's register on the network and get connected.
Step 1: Register on Rogers Network
text

AT+COPS=0

Wait 15 seconds.
Step 2: Check Registration Status
text

AT+CREG?

Tell me what number you see.
Step 3: Check Signal Strength
text

AT+CSQ

Step 4: Attach to Data Network
text

AT+CGATT=1

Wait 10 seconds, then:
text

AT+CGATT?

Should show +CGATT: 1
Step 5: Activate PDP Context
text

AT+CGACT=1,1

Wait 10 seconds.
Step 6: Get IP Address
text

AT+CGPADDR=1

This should show an IP address like 10.x.x.x or 100.x.x.x
Step 7: Check Full Status
text

AT+CREG?
AT+CGATT?
AT+CGPADDR=1

If Still Not Registering
Try IPv4 Only (Some modems don't like IPv6)
text

AT+CGDCONT=1,"IP","ltemobile.apn"
AT+COPS=0
AT+CGACT=1,1
AT+CGPADDR=1

Try Manual Network Selection
text

AT+COPS=1,2,"302720"
AT+CREG?

Reset Radio
text

AT+CFUN=0
AT+CFUN=1
AT+COPS=0
AT+CREG?

What to Look For

Successful Registration:

    +CREG: 0,1 or +CREG: 0,5 = Registered

    +CGATT: 1 = Attached to data

    +CGPADDR: 1,"10.x.x.x" = Got IP address

Still Not Working:

    +CREG: 2,8 = Still searching/no network

    +CGATT: 0 = Not attached

    +CGPADDR: 1 with no IP = Not connected

Quick Diagnostic

Please run these and paste the results:
text

AT+CREG?
AT+CGATT?
AT+CGPADDR=1
AT+CSQ

This will tell us exactly where we are in the connection process
