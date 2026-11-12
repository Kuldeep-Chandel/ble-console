# BLE Console

A browser-based BLE GATT terminal. Connect to an ESP32 (or any BLE device), send ASCII/HEX commands, watch notifications live, and stream files over GATT — no app, no install, no drivers.

**Live:** https://&lt;Kuldeep-Chandel&gt;.github.io/ble-console/

![BLE Console demo](demo.gif)

## Features

- **Connect** to any BLE device by service + characteristic UUID
- **Terminal** — send ASCII commands with selectable line ending (None / CR / LF / CR+LF)
- **Quick commands** — save one-click ASCII or HEX commands, fire with `Alt + 1…9`, import/export as JSON
- **File transfer** — stream any file in configurable chunks with progress and abort
- **Session log** — timestamped IN / OUT / SYS entries, text and type filters, export to `.log`
- Works with notify, indicate, write, and write-without-response characteristics

## Browser support

| Browser | Supported |
|---|---|
| Chrome / Edge / Opera — Windows, macOS, ChromeOS, Android | ✅ |
| Chrome — Linux | ✅ (may need `chrome://flags/#enable-experimental-web-platform-features`) |
| Firefox | ❌ No Web Bluetooth |
| Safari / any browser on iOS | ❌ — use the [Bluefy](https://apps.apple.com/app/bluefy-web-ble-browser/id1492822055) browser |

The page must be served over **HTTPS** (or `localhost`). Opening the file directly from disk will not work in most browsers.

## Quick start with ESP32

The default UUIDs match ESP-IDF's `gatt_server` example:

| | UUID |
|---|---|
| Service | `000000ff-0000-1000-8000-00805f9b34fb` (0x00FF) |
| Characteristic | `0000ff01-0000-1000-8000-00805f9b34fb` (0xFF01) |

1. Flash `examples/bluetooth/bluedroid/ble/gatt_server` from ESP-IDF (or your own firmware).
2. Open the console in Chrome and click **Establish Link**.
3. Pick your device from the browser's chooser.
4. Type a command and press **Enter**, or use a quick command.

For your own firmware, change the UUIDs in the Connection panel before connecting.

## File transfer and chunk size

Web Bluetooth does not expose the negotiated ATT MTU, so you set the chunk size yourself:

- **20 bytes** — always works (default MTU of 23)
- **MTU − 3** — e.g. `244` if your firmware negotiates MTU 247 (`esp_ble_gatt_set_local_mtu(247)` on ESP-IDF)

If uploads fail on the first chunk, lower the chunk size. The console sends raw file bytes with no framing — your firmware decides how to reassemble them.

## Quick command JSON format

```json
[
  { "label": "Status", "payload": "STATUS?", "format": "ascii" },
  { "label": "Ping",   "payload": "AA 55 01 00", "format": "hex" }
]
```

ASCII commands get the selected line ending appended. HEX commands are sent as raw bytes; spaces, commas, colons and `0x` are ignored.

## Running locally

Web Bluetooth needs a secure context, and `localhost` counts:

```bash
python -m http.server 8000
# open http://localhost:8000
```

## Privacy

Everything runs in your browser. No data is sent to any server; quick commands and preferences are stored only in your browser's local storage.

## License

MIT — see [LICENSE](LICENSE).

---

Built by **Embedded Knowledge** — ESP32 / ESP-IDF engineering.

