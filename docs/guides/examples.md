# Code Examples

## Sending a Hello World

### Transmitter

=== "USB Nugget"

    ```python
    # Send Hello World on the USB Nugget (ESP32s2)
    import board
    import busio
    import digitalio
    import adafruit_rfm9x

    # Initialize SPI interface.
    spi = busio.SPI(board.IO6, MOSI=board.IO10, MISO=board.IO8)

    # Initialize RFM9x LoRa radio
    CS = digitalio.DigitalInOut(board.IO13)
    RESET = digitalio.DigitalInOut(board.IO5)
    rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    rfm9x.tx_power = 23

    # Send text without specifiyng encoding
    message = "Hello World!"
    rfm9x.send(message)
    print("Sent Hello World string!")


    #Send the same text, specify utf-8 bytes
    rfm9x.send(bytes("Hello world!\r\n", "utf-8"))
    print("Sent Hello World bytes!")
    ```

=== "Bluetooth Nugget"

    ```python
    # Send Hello World on the Bluetooth Nugget (ESP32s3)
    import board
    import busio
    import digitalio
    import adafruit_rfm9x

    # Initialize SPI interface
    spi = busio.SPI(board.IO6, MOSI=board.IO8, MISO=board.IO7)

    # Initialize RFM9x LoRa radio
    CS = digitalio.DigitalInOut(board.IO9)
    RESET = digitalio.DigitalInOut(board.IO4)
    rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    rfm9x.tx_power = 23

    # Send text without specifiyng encoding
    message = "Hello World!"
    rfm9x.send(message)
    print("Sent Hello World string!")


    #Send the same text, specify utf-8 bytes
    rfm9x.send(bytes("Hello world!\r\n", "utf-8"))
    print("Sent Hello World bytes!")
    ```


### Receiver

=== "USB Nugget"

    ```python
    # Recieve Hello World on the USB Nugget (ESP32s2)
    import board
    import busio
    import digitalio
    import adafruit_rfm9x

    # Initialize SPI interface.
    spi = busio.SPI(board.IO6, MOSI=board.IO10, MISO=board.IO8)

    # Initialize RFM9x LoRa radio
    CS = digitalio.DigitalInOut(board.IO13)
    RESET = digitalio.DigitalInOut(board.IO5)
    rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    rfm9x.tx_power = 23

    while True:
        packet = rfm9x.receive()
        if packet is None:
            print("Received nothing! Listening again...")
        else:
            print("Received (raw bytes):", packet)
            # you always receive raw bytes and need to convert to a text
            # format like ASCII if you intend to do string processing.
            packet_text = str(packet, "ascii")
            print("Received (ASCII):", packet_text)
            # Also read the signal strength of the received message and print it.
            signal = rfm9x.last_rssi
            print("Received signal strength:", signal)
    ```

=== "Bluetooth Nugget"

    ```python
    # Recieve Hello World on the Bluetooth Nugget (ESP32s3)
    import board
    import busio
    import digitalio
    import adafruit_rfm9x

    # Initialize SPI interface
    spi = busio.SPI(board.IO6, MOSI=board.IO8, MISO=board.IO7)

    # Initialize RFM9x LoRa radio
    CS = digitalio.DigitalInOut(board.IO9)
    RESET = digitalio.DigitalInOut(board.IO4)
    rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    rfm9x.tx_power = 23

    while True:
        packet = rfm9x.receive()
        if packet is None:
            print("Received nothing! Listening again...")
        else:
            print("Received (raw bytes):", packet)
            # you always receive raw bytes and need to convert to a text
            # format like ASCII if you intend to do string processing.
            packet_text = str(packet, "ascii")
            print("Received (ASCII):", packet_text)
            # Also read the signal strength of the received message and print it.
            signal = rfm9x.last_rssi
            print("Received signal strength:", signal)
    ```


## Packet Repeater

=== "USB Nugget"

    ```python
    # Repeat a received packet on the USB Nugget (ESP32s2)

    import board
    import busio
    import digitalio
    import adafruit_rfm9x
    import time

    # Initialize SPI interface.
    spi = busio.SPI(board.IO6, MOSI=board.IO10, MISO=board.IO8)

    # Initialize RFM9x LoRa radio
    CS = digitalio.DigitalInOut(board.IO13)
    RESET = digitalio.DigitalInOut(board.IO5)
    rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    rfm9x.tx_power = 23

    # Send text without specifiyng encoding
    message = "Ping!"
    rfm9x.send(message)
    print("Sent Ping!")

    while True:
        packet = rfm9x.receive()
        # If no packet was received during the timeout then None is returned.
        if packet is None:
            print("Received nothing! Listening again...")
        else:
            print("Received (raw bytes):", packet)
            # you always receive raw bytes and need to convert to a text
            # format like ASCII if you intend to do string processing.
            packet_text = str(packet, "ascii")
            print("Received (ASCII):", packet_text)
            # Also read the signal strength of the received message and print it.
            signal = rfm9x.last_rssi
            print("Received signal strength:", signal)
            # Rebroadcast the packet
            time.sleep(1)
            print("Rebroadcasting packet...")
            rfm9x.send(packet_text)
            print("Sent!")
    ```

=== "Bluetooth Nugget"

    ```python
    # Repeat a received packet on the Bluetooth Nugget (ESP32s3)

    import board
    import busio
    import digitalio
    import adafruit_rfm9x
    import time

    # Initialize SPI interface
    spi = busio.SPI(board.IO6, MOSI=board.IO8, MISO=board.IO7)

    # Initialize RFM9x LoRa radio
    CS = digitalio.DigitalInOut(board.IO9)
    RESET = digitalio.DigitalInOut(board.IO4)
    rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    rfm9x.tx_power = 23

    # Send text without specifiyng encoding
    message = "Ping!"
    rfm9x.send(message)
    print("Sent Ping!")

    while True:
        packet = rfm9x.receive()
        # If no packet was received during the timeout then None is returned.
        if packet is None:
            print("Received nothing! Listening again...")
        else:
            print("Received (raw bytes):", packet)
            # you always receive raw bytes and need to convert to a text
            # format like ASCII if you intend to do string processing.
            packet_text = str(packet, "ascii")
            print("Received (ASCII):", packet_text)
            # Also read the signal strength of the received message and print it.
            signal = rfm9x.last_rssi
            print("Received signal strength:", signal)
            # Rebroadcast the packet
            time.sleep(1)
            print("Rebroadcasting packet...")
            rfm9x.send(packet_text)
            print("Sent!")
    ```
