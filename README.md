# Smart-Home-Display (ESPHome + LVGL)

Eigenes ESPHome+LVGL-Konfig für ein Waveshare ESP32-P4 4-Zoll-Touchdisplay als Home-Assistant-Wallpanel — eingerichtet für meine Wohnung.

## Hardware

- **Board**: [Waveshare ESP32-P4-WIFI6-Touch-LCD-4B](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-4b.htm)
- **Display**: 4″ MIPI-DSI, 720×720
- **Touch**: GT911 (I²C)
- **Wi-Fi**: ESP32-C6 Coprocessor über SDIO

Konfig steckt komplett im Subordner [`waveshare-esp32-p4-wifi6-touch-lcd-4b/`](waveshare-esp32-p4-wifi6-touch-lcd-4b/).

## Funktionen

- **Startseiten-Grid** mit Buttons für Szenen, 3D-Drucker, Staubsauger, Eve-Thermostat (mit ±0.5 °C-Tasten und Power-Toggle).
- **Solar-PV-Visualisierung** — Plus-Layout mit Flussrichtungs-Pfeilen (Sonnenbatterie-Daten) und alternativer kompakter Listenansicht (Toggle oben rechts).
- **Detail-Pages** für Heizung, 3D-Drucker, Staubsauger, Szenen.
- **Notification-Center** (Top-Layer-Overlay, max. 6 Slots, Auto-Expire).
- **Greeting-Page** mit großer Uhr, Tageszeit-Greeter (+ Name aus HA), 4-Slot-Stunden-Forecast.
- **HA-Fernsteuerung**: Aktive Seite per Select wählen, Default-Seite konfigurieren, Auto-Revert nach Idle (Zeit einstellbar) / nach Display-Off ein- oder ausschalten — alles über HA-Entities exposed.
- **Tap-to-Wake** + Präsenz-Sensor-gesteuerter Bildschirmschoner.

## Setup

1. **ESPHome-Konfig in HA-Dashboard anlegen** mit Inhalt von [`waveshare-esp32-p4-wifi6-touch-lcd-4b/esphome.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/esphome.yaml). `name`, `friendly_name`, `room` in den Substitutions anpassen.
2. **`secrets.yaml`** im ESPHome-Dashboard pflegen — Variablen `wifi_ssid` und `wifi_password`.
3. **Greeting-Page-Forecast**: 12 Template-Sensoren in HA anlegen (Snippet im `template:`-Stil mit `weather.get_forecasts`-Trigger). Siehe Commit-Historie / Plan-Datei.
4. **Solar-Page**: erwartet die `sensor.sonnenbatterie_217105_state_*`-Entities aus dem Sonnenbatterie-HA-Plugin.
5. **Heizung**: `climate.eve_thermo_20ebp1701`. Bei abweichender Entity-ID die drei Stellen in `device/sensors.yaml` und die beiden `homeassistant.service`-Blöcke in `device/lvgl.yaml` anpassen.
6. Compilen & Flashen.

## Customizing

- **Theme-Farben/Fonts**: [`theme/button.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/theme/button.yaml) und [`assets/fonts.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/assets/fonts.yaml).
- **MDI-Glyphen**: [`assets/icons.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/assets/icons.yaml) — Substitutions-Map am Ende, Codepunkte aus [materialdesignicons.com/cdn/7.4.47/](https://materialdesignicons.com/cdn/7.4.47/).
- **Page-Layouts**: [`device/lvgl.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/device/lvgl.yaml).
- **HA-Bindings**: [`device/sensors.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/device/sensors.yaml).
- **HA-Controls** (Selects/Switches/Number/Text): [`addon/ha_controls.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/addon/ha_controls.yaml).

## Credits

Basis war [`jtenniswood/esphome-lvgl`](https://github.com/jtenniswood/esphome-lvgl) — die Grundkonfiguration der hardware stammen daher.
