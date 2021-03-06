# pi-fan-pwm

Scripts used to run a PWM fan according to Raspberry CPU temperature.

Tested with Noctua NF-A4x10 5V PWM,  Raspberry Pi 4 and common small heatsinks.

No extra hardware required, unless a 1N4148 diode between +3.3V(K) and the fan PWM input(A), to protect that GPIO pin.

At almost no CPU load, fan is silent, runs at 2.5k RPM, and is not much faster/louder for CPU at 100%.

## Installation

1. Clone down this repo to your Raspberry Pi
2. Connect fan +5V/GND to the Raspberry, fan will run at max speed
3. Connect fan PWM pin to GPIO 18, tacho pin to GPIO 23
4. Run and tweak duty cycles plus temperature thresholds in `pi-fan-pwm.py`
4. Use `stress-ng --cpu 4` plus `watch tools/temp.sh` to monitor your CPU temperature
5. Run `script/install`

That's it!

## Adding optionnal luma.oled display scripts

Add a 128x64 OLED display to the Pi SPI port and install luma. See https://github.com/rm-hull/luma.oled

Once the repo cloned and luma installed, run carousel.py script (example with the SH1106 driver):

`python ./luma/carousel.py --spi-port 0 --spi-device 0  --display sh1106 --interface spi`

pi-fan-pwm.py updates /run/pi-fan-pwm.out with the fan duty cycle and RPM. Data is read and displayed by cpustate widget.

For installation:
1. Adjust `pi-fan-pwm.carousel.sh` content for your display model
2. Run `script/install.carousel`

luma/carousel.py was adapted from https://github.com/rm-hull/luma.examples carousel.py script.

## SPI bus wiring

- GND to GND
- VCC to either 3.3V or 5V (depending on OLED model)
- SCL/SCLK to BCM 11
- SDA/MOSI to BCM 10
- RES(ET) to BCM 25
- DC to BCM 24
- CS to BCM8 (CE0)

## About FAN_FREQ = 100

According to Intel standards, fan PWM signal shall be 25kHz. But RPi.GPIO currently doesn't support hardware PWM.

At 25kHz, pi-fan-pwm.py eats up to 20% of a CPU core, due to software PWM.

But at 100Hz, CPU usage is only less than 1%, and this seems suported by Noctua NF-A4x10 5V fan.
