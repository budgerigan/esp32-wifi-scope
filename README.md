# ESP32 Wi-Fi Scope

A portable, passive 2.4 GHz Wi-Fi analyzer built around a classic ESP32,
an SSD1306 OLED, and two buttons. The firmware will use C, ESP-IDF, and FreeRTOS.

**Status: architecture and repository bootstrap. Firmware, tests, measurements,
and hardware validation are not implemented yet.**

The purpose of this portfolio project is to demonstrate bounded memory use,
safe binary parsing, concurrent firmware design, fault handling, and reproducible
measurements. It uses Espressif's Wi-Fi driver; it does not implement an RF driver.

## Planned v1

- Passive channel survey, with an initial 250 ms dwell on channels 1–11.
- Continuous monitoring of a selected channel or access point.
- Up to 64 observed APs: SSID, BSSID, advertised channel, RSSI, and last seen.
- Observed management/data/control frame counts and rates normalized by listening time.
- Three OLED views and two-button navigation.
- USB-UART commands and periodic JSON Lines telemetry; a Python JSONL/CSV collector.
- Explicit queue overflow, parser error, memory, and uptime diagnostics.

This is a Wi-Fi frame observer, not a general RF spectrum analyzer. Observed
frame/byte rates are not total channel utilization, Internet speed, or a complete
count of all traffic. Application drops do not measure unknown radio/driver losses.

Web UI, packet injection, PCAP, SD storage, batteries, OTA, CSI, and RSSI graphs
are outside v1.

## Documentation

The implementation guide is currently in Russian:

- [Architecture](docs/architecture.md): module boundaries, ownership, state transitions, and measurement semantics.
- [Roadmap and acceptance criteria](docs/roadmap.md): incremental milestones and evidence required for v1.
- [First hardware step](docs/first-steps.md): board identification and the first ESP-IDF build.

## Hardware and toolchain target

| Item | Baseline |
| --- | --- |
| MCU | Classic ESP32 / ESP32-WROOM development board, exact board pending confirmation |
| SDK | ESP-IDF **v5.5.6**, target `esp32` |
| Firmware | C and FreeRTOS |
| Display | SSD1306, 128×64, I²C, framebuffer through `esp_lcd` |
| Input | Two momentary buttons |
| Power and host link | USB power and the board's USB-UART bridge |
| Host tests | Native C tests, ASan/UBSan, and parser fuzzing — planned |

No build or flashing command for this repository is available yet. The first
milestone uses Espressif's `hello_world` example in a separate directory; see
[first steps](docs/first-steps.md). A firmware build command will be added when
the initial ESP-IDF application exists.

## Planned source layout

These source directories will be introduced when their first implementation is
added; they are not placeholder implementations:

```text
main/                        application startup and task wiring
components/scope_core/       portable C parser, aggregation, AP table
components/scope_capture/    Wi-Fi adapter, packet pool, capture queue
components/scope_control/    mode and channel ownership
components/scope_ui/         OLED and buttons
components/scope_serial/     commands and telemetry
tools/                      host collector
tests/host/                 portable C tests and fuzz harness
.github/workflows/          firmware build and host-test CI
```

## Evidence and releases

There is no v1.0 release or benchmark result yet. A release requires parser tests,
overload recovery, 100 mode transitions, an eight-hour hardware run, and a
documented experiment with a controlled AP. Actual measurements and a short demo
will be added after those experiments, with board, SDK version, and commit recorded.

## References

- [ESP-IDF v5.5.6 release](https://github.com/espressif/esp-idf/releases/tag/v5.5.6)
- [ESP32 Wi-Fi sniffer documentation](https://docs.espressif.com/projects/esp-idf/en/v5.5.6/esp32/api-guides/wifi.html#wi-fi-sniffer-mode)
- [ESP32-DevKitC hardware reference](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32/esp32-devkitc/user_guide.html)
- [ESP-IDF LCD documentation](https://docs.espressif.com/projects/esp-idf/en/v5.5.6/esp32/api-reference/peripherals/lcd/index.html)
