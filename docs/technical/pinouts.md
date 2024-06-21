## Pinout Schematics

=== "Top"

    ![Top Pinout](../assets/Pinout_LoRa_Paw_V2_Top.png)
    Top Pinout of the LoRa Paw

=== "Bottom"

    ![Bottom Pinout](../assets/Pinout_LoRa_Paw_V2_Bottom.png)
    Bottom Pinout of the LoRa Paw


## Connecting the LoRa board to the Nugget

=== "USB Nugget"

    ```python
      # Initialize SPI interface
      spi = busio.SPI(board.IO6, MOSI=board.IO10, MISO=board.IO8)

      # Initialize RFM9x LoRa radio
      CS = digitalio.DigitalInOut(board.IO13)
      RESET = digitalio.DigitalInOut(board.IO5)
      rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    ```

=== "Bluetooth Nugget"

    ```python
      # Initialize SPI interface
      spi = busio.SPI(board.IO6, MOSI=board.IO8, MISO=board.IO7)

      # Initialize RFM9x LoRa radio
      CS = digitalio.DigitalInOut(board.IO9)
      RESET = digitalio.DigitalInOut(board.IO4)
      rfm9x = adafruit_rfm9x.RFM9x(spi, CS, RESET, 915.0)
    ```
