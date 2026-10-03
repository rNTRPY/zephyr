.. zephyr:board:: rntrpy_r4vn

Overview
********

The rNTRPY R4VN is an nRF52840-based LoRa sensor node. It combines a Nordic
nRF52840 microcontroller with a Semtech SX1262 LoRa transceiver, a u-blox
MAX-M10S GNSS receiver, an on-board environmental/motion sensor set, and a
Li-Po battery and charger subsystem. It is compatible with Meshtastic and
LoRaWAN.

The features include the following:

- Microprocessor: Nordic nRF52840 (ARM Cortex-M4F, 64 MHz, 256 KB RAM, 1 MB flash)
- LoRa node chip: Semtech SX1262
- GNSS module: u-blox MAX-M10S (NMEA over UART)
- Sensors on I2C-1: Bosch BMA400 accelerometer, Vishay VEML7700 ambient light
  sensor, Maxim MAX17048 fuel gauge, FocalTech FT6336U e-paper touch controller
- Second I2C bus (I2C-2) reserved for a Sensirion SCD30 CO2 sensor
- SK6812 RGBW addressable LED strip (5 LEDs)
- USB-C interface with USB device (CDC ACM) support
- BLE 5.0, IEEE 802.15.4 (OpenThread)
- Power and reset buttons
- BQ24074 Li-Po charger with USB-C CC sensing
- GPIO-gated power rails for the LED strip, GNSS and SCD30

Supported Features
==================

.. zephyr:board-supported-hw::

Connections and IOs
===================

LED
---

* SK6812 RGBW LED strip (5 LEDs), data on P0.03; powered from the ``reg_5v`` rail

Push buttons
------------

* Power button = P1.11 (``sw0``)
* Reset button = P0.18 (nRESET)

Power rails
-----------

The LED strip, GNSS receiver and SCD30 sit behind GPIO-controlled load switches,
exposed as ``regulator-fixed`` nodes (``reg_5v``, ``reg_gps``, ``reg_scd30``).
They are off at boot; enable a rail with :c:func:`regulator_enable` before using
the peripheral behind it and disable it afterwards to save power.

E-paper connector
-----------------

The 24-pin FPC takes SSD16xx e-paper panels (e.g. GoodDisplay GDEY029T94)
in 4-wire SPI mode on SPIM2. The board provides the
``epd_dbi`` MIPI DBI bus; the application adds the panel node to it.

* SCK = P0.06, MOSI = P0.12, CS = P0.26
* D/C = P0.07, RST = P0.16, BUSY = P0.15
* Frontlight = P0.21, 1 kHz PWM on PWM0, active high (``frontlight``)
* Touch = FocalTech FT6336U on I2C-1 at 0x38 (``touch``); INT and RST are not
  connected, so the ``focaltech,ft5336`` driver polls

System Requirements
*******************

- Host machine with USB port
- USB-C cable for programming and serial console (CDC ACM)
- Optional: J-Link or other SWD debugger for debugging
- Optional: UF2 bootloader for drag-and-drop flashing; build with the
  ``rntrpy_r4vn/nrf52840/uf2`` board target

Programming and Debugging
*************************

.. zephyr:board-supported-runners::

The default ``rntrpy_r4vn/nrf52840`` target uses the standard Nordic nRF52840
partition layout (full flash image). Use the ``rntrpy_r4vn/nrf52840/uf2`` variant
when building for the board's UF2 bootloader and its application partition layout.

Applications for the ``rntrpy_r4vn/nrf52840`` board configuration can be built,
flashed, and debugged in the usual way. See :ref:`build_an_application` and
:ref:`application_run` for more details on building and running.

Flashing
========

First, run your favorite terminal program to listen for output (e.g. 115200 baud).
Then build and flash. Example for the :zephyr:code-sample:`hello_world` application:

.. code-block:: console

   west build -b rntrpy_r4vn/nrf52840 samples/hello_world
   west flash

For the UF2 bootloader variant (drag-and-drop firmware):

.. code-block:: console

   west build -b rntrpy_r4vn/nrf52840/uf2 samples/hello_world
   west flash

Debugging
=========

Debugging is supported via SWD. Use an nRF52-compatible probe (e.g. nRF52840 DK,
J-Link) and connect SWDIO, SWDCLK, GND, and VDD. Refer to the :ref:`nordic_segger`
page to learn about debugging Nordic boards with a Segger IC. OpenOCD and Zephyr's
debug runner can also be used with the appropriate board configuration.

References
**********

.. target-notes::

.. _`nRF52840 Product Specification`: https://docs.nordicsemi.com/bundle/ps_nrf52840/page/keyfeatures_html5.html
