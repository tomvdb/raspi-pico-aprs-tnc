# raspi-pico-aprs-tnc
A TX-only [TNC](https://en.wikipedia.org/wiki/Terminal_node_controller) (Terminal Network Controller) to generate the [AFSK](https://en.wikipedia.org/wiki/Frequency-shift_keying#Audio_FSK) audio tones for [APRS](https://en.wikipedia.org/wiki/Automatic_Packet_Reporting_System) (Automatic Packet Reporting System) messages using a [Raspberry Pi Pico](https://en.wikipedia.org/wiki/Raspberry_Pi) microcontroller board.

An analog line-out audio signal will be generated by a filter connected to GPIO-pin 'GP0' which provides the binary PWM signal. You can probe it by a scope, listen to it by using an audio amp, or connect it to any RF transceiver to send it on the air (ham radio license required).

![AFSK scope screenshot](https://github.com/eleccoder/raspi-pico-aprs-tnc/blob/main/doc/img/afsk_scope.png)

Image: Line-out signal (see [below](#Hardware)) probed by a DSO. We clearly see the 1200 Hz and 2200 Hz tones of the 1200 Bd 2-AFSK.

Basically, the data/signal flow is as follows:

```
APRS (text msg + geo-coordinates + meta-data) -> AX.25 -> PCM -> PWM -> Band-Pass filtering -> AFSK audio signal
```

Both a static library `libaprs_pico.a` and an example application will be generated by the build.


## Preliminaries

Your host platform for cross-compilation is assumed to be LINUX.

1. Install the Pico-SDK following the instructions given in the [Raspberry Pi 'Getting Started' Guide](https://datasheets.raspberrypi.org/pico/getting-started-with-pico.pdf) (pdf)
1. Set the `PICO_SDK_PATH` environment variable to point to your Pico-SDK installation directory

## Hardware

We just need a simple band-pass filter to extract the analog AFSK-signal from the binary PWM signal:

![band-pass filter](https://github.com/eleccoder/raspi-pico-aprs-tnc/blob/main/doc/img/band_pass_filter.png)

The line-out voltage can be as high as 2.7 V<sub>pp</sub> (~1 V<sub>rms</sub>) (at full-scale volume setting in the software and high-impedance load).

## Build the library and the example application

```
(cd into the cloned dir)
cmake -S . -B build
cmake --build build
```

`build/lib/libaprs_pico.a` and `build/aprs_pico_example.uf2|.elf|.xxx` will be generated.

## Run the example application

```
cd build
(flash 'aprs_pico_example.uf2' or 'aprs_pico_example.elf' to the Pico board as usual)
```

The analog AFSK audio signal will be available at the filter's line-out. You can probe it by a scope, listen to it by using an audio amp, or connect it to any RF transceiver to send it on the air (ham radio license required).

## TODO (Aug 2021)

- [x] Thorough evaluation, in general
- [x] Send the APRS message on the console (USB or UART) rather than hard-coding
- [x] Show how to physically connect to a Baofeng HT
- [x] PTT control for RF transceivers

## Ingredients / Acknowledgements

- For APRS => AX.25 => PCM conversion I'm using [my modified version](https://github.com/eleccoder/ax25-aprs-lib) of [fsphil's ax25beacon](https://github.com/fsphil/ax25beacon)
- For PCM => PWM conversion I'm using the audio lib from [pico-extras](https://github.com/raspberrypi/pico-extras) (WARNING: maturity seems to be alpha/beta)
