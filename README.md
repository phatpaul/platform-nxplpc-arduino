# platform-nxplpc-arduino

PlatformIO development platform for NXP LPC8xx (LPC804, LPC845) Cortex-M0+
boards with the **Arduino** framework.

This is a focused fork of
[platformio/platform-nxplpc](https://github.com/platformio/platform-nxplpc)
that adds the Arduino framework binding so the
[ArduinoCore-LPC8xx](https://github.com/zackees/ArduinoCore-LPC8xx) package
can drive a PlatformIO build. The upstream platform only ships mbed and
zephyr framework bindings; this fork drops those and adds `arduino`.

## Usage

```ini
[env:lpc845brk]
platform = https://github.com/zackees/platform-nxplpc-arduino.git
framework = arduino
board = lpc845brk
```

PlatformIO installs:

- The `platform-nxplpc-arduino` platform (this repo).
- The ARM GCC toolchain (`toolchain-gccarmnoneeabi`).
- The `framework-arduino-lpc8xx` package (the ArduinoCore-LPC8xx repo).

Board manifests (`boards/lpc845brk.json`, `boards/lpcxpresso804.json`,
`boards/lpcxpresso845max.json`) live in the ArduinoCore-LPC8xx repo and
are auto-discovered from the project root.

## What the Arduino binding does

`builder/frameworks/arduino.py` is a ~150-line SCons script that:

1. Locates the installed `framework-arduino-lpc8xx` package and asserts
   `cores/<core>/` + `variants/<variant>/` directories from `boards/<id>.json`.
2. Sets compile / link flags matching the package's upstream `platform.txt`
   so PlatformIO and arduino-cli produce comparable output.
3. Adds Arduino defines (`ARDUINO=10819`, `ARDUINO_ARCH_LPC8XX`,
   `ARDUINO_<BOARD>`, `F_CPU`) plus the board's `build.extra_flags`.
4. Picks the linker script from `board_build.ldscript` (or the board JSON
   default).
5. Builds variant + core as static libs and prepends them to LIBS.

## Why a fork rather than upstream PR?

The upstream `nxplpc` platform supports a different set of LPC families
(LPC1768, LPC11U68, LPC54114, etc.) for mbed/zephyr. Adding Arduino with
LPC8xx-specific values directly upstream would be a bigger surface than
the upstream maintainers may want without a coordinated proposal. This
fork is a quick path to a working PlatformIO + fbuild story; an upstream
PR can follow once the LPC8xx Arduino path is exercised in CI.

## License

Apache-2.0 (matches upstream).
