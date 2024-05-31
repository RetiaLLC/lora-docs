# LoRa Rickroll

Ever wanted to rickroll your friends from a long distance? 

Here's Kody doing it, after that, you give it a try!

<iframe width="560" height="315" src="https://www.youtube.com/embed/AM9fhVpHauk?si=Ew1OnSH3sqX5tO4e" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## Setting up the environment
The LoRa Paw can easily be interfaced using CircuitPython. To flash it to your nugget, simply head over to our [flashing site](https://nugget.dev).

From there, our next step is to install the dependencies.

## Satisfying dependency requirements

This project requires the following libraries: `adafruit_rfm9x`, `adafruit_displayio_sh1106`, `adafruit_hid`, and `adafruit_ducky`.

You can download these from [CircuitPython's website](https://circuitpython.org/libraries). Note you will need Bundle version `9.x`.


## Coding the Rickroll

In order to perform a rickroll over Lora, several things must happen:
- Broadcasting device sends trigger code
- Receiving device picks up trigger code
- Receiving device executes Catscratch payload

Luckily for us, this is a fairly simple task that involves modifying `code.py`, and creating a duckyscript payload (`payload.txt`).

!!! warning
    Pinouts vary between USB and Bluetooth Nuggets. Be sure it matches the [pinouts](../technical/pinouts.md)

```python title="code.py"
# SPDX-FileCopyrightText: 2024 ladyada for Adafruit Industries & Skicka
# SPDX-License-Identifier: MITs

# Simple demo of sending and recieving data with the RFM95 LoRa radio.
# Author: Tony DiCola / Kody Kinzie
import board
import busio
import digitalio
import neopixel
import adafruit_rfm9x
import displayio
import terminalio
from adafruit_display_text import label
import busio
from adafruit_displayio_sh1106 import SH1106
import neopixel
import time
import usb_hid
from adafruit_hid.keyboard import Keyboard
from adafruit_hid.keyboard_layout_us import KeyboardLayoutUS
import usb_hid
from adafruit_hid.keyboard import Keyboard
from adafruit_hid.keyboard_layout_us import KeyboardLayoutUS
import adafruit_ducky

time.sleep(1)  # Sleep for a bit to avoid a race condition on some systems
keyboard = Keyboard(usb_hid.devices)
keyboard_layout = KeyboardLayoutUS(keyboard)  # We're in the US :)
duck = adafruit_ducky.Ducky("payload.txt", keyboard, keyboard_layout)

def RickMe():
    result = True
    while result is not False:
        result = duck.loop()

# Release any resources currently in use for the displays
displayio.release_displays()

i2c = busio.I2C(scl=board.SCL, sda=board.SDA)
display_bus = displayio.I2CDisplay(i2c, device_address=0x3C)

# Define the width and height of the display
WIDTH = 130
HEIGHT = 64
display = SH1106(display_bus, width=WIDTH, height=HEIGHT)



pixel = neopixel.NeoPixel(board.IO12, 4, brightness=0.2)
pixel[0] = (0,0,255)  # equivalent


# Define radio parameters.
RADIO_FREQ_MHZ = 915.0  # Frequency of the radio in Mhz. Must match your
# module! Can be a value like 915.0, 433.0, etc.

# Define pins connected to the chip, use these if wiring up the breakout according to the guide:
CS = digitalio.DigitalInOut(board.IO13)
RESET = digitalio.DigitalInOut(board.IO5)
# Or uncomment and instead use these if using a Feather M0 RFM9x board and the appropriate
# CircuitPython build:
# CS = digitalio.DigitalInOut(board.RFM9X_CS)
# RESET = digitalio.DigitalInOut(board.RFM9X_RST)


# Initialize SPI bus.
spi = busio.SPI(board.IO6, MOSI=board.IO10, MISO=board.IO8)

# Initialze RFM radio
rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, RADIO_FREQ_MHZ)

# Note that the radio is configured in LoRa mode so you can't control sync
# word, encryption, frequency deviation, or other settings!

# You can however adjust the transmit power (in dB).  The default is 13 dB but
# high power radios like the RFM95 can go up to 23 dB:
rfm9x.tx_power = 23

# Send a packet.  Note you can only send a packet up to 252 bytes in length.
# This is a limitation of the radio packet size, so if you need to send larger
# amounts of data you will need to break it into smaller send calls.  Each send
# call will wait for the previous one to finish before continuing.
pixel[0] = (255,0,0)  # equivalent
rfm9x.send(bytes("Ping", "utf-8"))
print("Sent Ping message!")
pixel[0] = (0,0,255)  # equivalent
# Wait to receive packets.  Note that this library can't receive data at a fast
# rate, in fact it can only receive and process one 252 byte packet at a time.
# This means you should only use this for low bandwidth scenarios, like sending
# and receiving a single message at a time.
#print("Waiting for packets...")

rxCounter = 0

while True:
    packet = rfm9x.receive()
    # Optionally change the receive timeout from its default of 0.5 seconds:
    # packet = rfm9x.receive(timeout=5.0)
    # If no packet was received during the timeout then None is returned.
    if packet is None:
        # Packet has not been received
        pixel[0] = (0,0,255)  # equivalent
        #print("Received nothing! Listening again...")
    else:
        # Received a packet!
        rxCounter = rxCounter + 1
        pixel[0] = (0,255,0)  # equivalent
        # Print out the raw bytes of the packet:
        print("Packet Count: {}".format(rxCounter))
        # And decode to ASCII text and print it too.  Note that you always
        # receive raw bytes and need to convert to a text format like ASCII
        # if you intend to do string processing on your data.  Make sure the
        # sending side is sending ASCII data before you try to decode!
        packet_text = str(packet, "ascii")
        print("Msg: {0}".format(packet_text))
        # Also read the RSSI (signal strength) of the last received message and
        # print it.
        rssi = rfm9x.last_rssi
        print("RSSI: {0} dB".format(rssi))
        if "rickme" in packet_text:
            RickMe()
        #time.sleep(3)
```


And finally, our payload:

```plaintext title="payload.txt"
DELAY 1000
GUI SPACE
DELAY 1000
STRING terminal.app
ENTER
DELAY 1000
STRING open "https://youtu.be/dQw4w9WgXcQ"
ENTER
DELAY 5000
SPACE
```