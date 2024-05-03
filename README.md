# LILYGO T-Display ESP32 \[16MB\] \[CH9102F\] \[D0WDQ6-V3\]
Just got yourself a LILYGO T-Display ESP32? Got no idea where to start? This repository has some information you may find useful.

![Image of a LILYGO T-Display ESP32](/images/lilygo-t-display-esp32.png)

## Official store page
https://www.lilygo.cc/products/lilygo%C2%AE-ttgo-t-display-1-14-inch-lcd-esp32-control-board?variant=42159376466101

 - Resolution: 135 x 240
 - High Density 260 PPI
 - 4-Wire SPI interface
 - Working Power Supply: 3.3V
 - 1.14" diagonal
 - Full-color TFT Display
 - Drive: ST7789



## AliExpress
https://www.aliexpress.com/item/1005005970553639.html

T-Display ESP32 WiFi And Bluetooth-Compatible Module Development Board 1.14 Inch LCD Control for Arduino  
Color/variation: CH9102F 16MB

Specifications:
 - Item: T-Display ESP32 WiFi for Bluetooth Dual Module
 - Color: Black
 - Display: 1.14 inch colorful LCD display
 - Application: For Arduino, for IOT
 - Voltage: 3.7V

Power Supply Specifications:
 - Power Supply: USB 5V/1A
 - Charging current: 500mA
 - Battery: 3.7V lithium battery
 - JST Connector: 2Pin 1.25mm
 - USB: Type-C

Wi-Fi:
 - Standard: FCC/CE-RED/IC/TELEC/KCC/SRRC/NCC
 - Protocol: 802.11 b/g/n(802.11n, speed up to 150Mbps) A-MPDU and A-MSDU polymerization, support 0.4uS Protection interval
 - Frequency range: 2.4GHz\~2.5GHz (2400M\~2483.5M)
 - Transmit power: 22dBm
 - Communication distance: 300m



## Specifications
```
# From running example script 'GetChipID' in Arduino IDE:
ESP32 Chip model = ESP32-D0WDQ6-V3 Rev 3
This chip has 2 cores
Chip ID: 13505300
```


### Chip markings
 - `ESP32-D0WDQ6 V3 332023 UG00MAH070`
 - `CH9102F 1020FD39`



## Display size info
Standard orientation:
 - Max width:  `135px`
 - Max height: `240px`

Portrait orientation:
 - Max width:  `240px`
 - Max height: `135px`

It seems the TFT display requires an offset. These are the values I have been using:
 - tft_offset_x: `40`
 - tft_offset_y: `52`



## MicroPython


### LoBo wiki
 - https://github.com/loboris/MicroPython_ESP32_psRAM_LoBo/wiki
 - https://github.com/loboris/MicroPython_ESP32_psRAM_LoBo/wiki/display
 - https://github.com/loboris/MicroPython_ESP32_psRAM_LoBo/wiki/pin

### External guides
 - https://www.instructables.com/Installing-Loboris-lobo-Micropython-on-ESP32-With-/
 - https://www.youtube.com/watch?v=5hHeDV9GqgY


### Flashing MicroPython
```
+--[Heads up!]----------------------------------------------------[2024-05-03]--+
| Keep in mind that 'loboris/MicroPython_ESP32_psRAM_LoBo' is very out of date. |
| The last build was from 2018. I will still suggest this firmware for this     |
| type of microcontroller because it has pre-installed modules required for the |
| display to function. Meanwhile I'm testing official MicroPython firmwares to  |
| see what works.                                                               |
|                                                                               |
| When I figure out how to set up a fresh MicroPython firmware similarly, I     |
| will update the README with an additional guide (unless I give up or forget). |
+-------------------------------------------------------------------------------+
```
Install Arduino IDE and install necessary drivers when prompted.  
https://www.arduino.cc/en/software

Install esptool with PIP.  
https://github.com/espressif/esptool
```
python -m pip install --upgrade pip
pip install esptool
```

https://github.com/loboris/MicroPython_ESP32_psRAM_LoBo/wiki/firmwares  
Scroll down and download 'MicroPython_LoBo_esp32_all'

1. Make a new folder.
2. Open 'MicroPython_LoBo_esp32_all.zip'
3. Extract '/flash.sh' to the root of the folder you created.
4. Extract '/esp32_all/*' to the root of the folder you created.

Open a terminal, CD to the directory you created and type: (COM4 may be different)
```
esptool --chip esp32 --port COM4 --baud 460800 --before default_reset --after no_reset write_flash -z --flash_mode dio --flash_freq 40m --flash_size detect 0x1000 bootloader/bootloader.bin 0xf000 phy_init_data.bin 0x10000 MicroPython.bin 0x8000 partitions_mpy.bin
```

Wait for the process to complete.


### MicroPython for generic ESP32

https://micropython.org/download/ESP32_GENERIC/  
The ESP32 TTGO T-Display is not supported here. See section above.  
Use this if you have a generic ESP32 without TTGO T-Display.  
I have not tested any of these yet.


### IDE
 - Thonny:
   - https://thonny.org/
 - VSCode:
   - Install `PyMakr` extension by Pycom
   - Install `Black Formatter` extension by Microsoft

Add this to your VSCode user config:
```json
"[python]": {
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "ms-python.black-formatter",
  "editor.codeActionsOnSave": {
    "source.organizeImports": "always",
  }
}
```



## Example: How to use the TFT display
![Demo of the example code](/images/example-demo.png)

```python
import display
import sys

# Initialize display
tft = display.TFT()
tft.init(
    tft.ST7789,
    rst_pin=23,
    backl_pin=4,
    miso=0,
    mosi=19,
    clk=18,
    cs=5,
    dc=16,
    width=235,
    height=340,
    rot=tft.LANDSCAPE,
    backl_on=1,
)

# Fix inverted colors;
tft.tft_writecmd(0x21)

# Screen offset
tft_offset_x = 40
tft_offset_y = 52

# Available pixels
tft_width = 240
tft_height = 135

# Screen rotation and size
tft.orient(tft.LANDSCAPE)
tft.setwin(
    tft_offset_x,
    tft_offset_y,
    tft_offset_x + tft_width - 1,
    tft_offset_y + tft_height - 1,
)

# Set font
tft.font(tft.FONT_Small)


def clear():
    tft.clear(0x000000)


def get_line_offset(line):
    line_offset = 0
    if line > 1:
        line_offset = 12 * (line - 1)
    return line_offset


def print_line(text, line=1, text_color=0xCCCCCC, prefix="", prefix_color=0x1EDC62):
    prefix_offset = tft.textWidth(prefix)
    line_offset = get_line_offset(line)

    tft.text(0, line_offset, prefix, prefix_color, transparent=True)
    tft.text(prefix_offset, line_offset, text, text_color, transparent=True)


def print_lines(lines, text_color=0xCCCCCC, prefix_color=0x1EDC62):
    for index, line in enumerate(lines):
        print_line(
            line["text"],
            index + 1,
            prefix=line["prefix"],
            text_color=text_color,
            prefix_color=prefix_color,
        )


clear()
print_lines(
    [
        {"text": "", "prefix": ""},
        {"text": "Aleksander Haugmo", "prefix": " > "},
        {"text": "https://aleksander.dev", "prefix": " > "},
        {"text": "MicroPython v" + sys.version, "prefix": " > "},
    ],
    text_color=0x007ABB,
    prefix_color=0xFF0000,
)
```



## Example: How to use GPIO IRQ (interrupt requests) and handler functions
This example is specific for T-Display ESP32. It has buttons on `GPIO0` and `GPIO35`.  
Pins `34~39` do not have pull-up or pull-down circuitry.

To get `GPIO35` to trigger only when PRESSED, we have to code a switch. See `only_press_handler`.

You can read more about Pins here:  
https://github.com/loboris/MicroPython_ESP32_psRAM_LoBo/wiki/pin

Using IRQ (interrupt requests) we can trigger handler functions even when the program is busy doing something else.
Here, handler functions are triggered when the button is pressed. The main code is busy doing a loop but the program
is interrupted and our handler runs regardless.

```python
import machine
import time
from machine import Pin


def simple_handler(p):
    print("Inside 'simple_handler'")
    print(p)


pressed = False


def only_press_handler(p):
    global pressed
    if pressed:
        pressed = False
        return
    pressed = True

    print("Inside 'only_press_handler'")
    print(p)


# Handler is called when GPIO0 button is DOWN
# pin = Pin(0, Pin.IN, Pin.PULL_UP, handler=simple_handler, trigger=Pin.IRQ_FALLING)

# Handler is called when GPIO35 button is RELEASED
# pin = Pin(35, Pin.IN, handler=simple_handler, trigger=Pin.IRQ_RISING)

# Handler is called when GPIO35 button is DOWN and RELEASED
# pin = Pin(35, Pin.IN, handler=simple_handler, trigger=Pin.IRQ_FALLING)

# Handler is called when GPIO35 button is DOWN (fixed behavior with code)
# There might be a slight chance the handler is triggered a third time.
# You can minimize the chance of the coded switch inverting, by adding
# some 'acttime' (defined in microseconds).
pin = Pin(35, Pin.IN, handler=only_press_handler, trigger=Pin.IRQ_FALLING)

for i in range(100):
    print(pin.value())
    time.sleep(0.1)
```
