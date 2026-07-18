# Smart-Home-Display (ESPHome + LVGL)

Eigenes ESPHome+LVGL-Konfig für ein Waveshare ESP32-P4 4-Zoll-Touchdisplay als Home-Assistant-Wallpanel — eingerichtet für meine Wohnung.

## Hardware

- **Board**: [Waveshare ESP32-P4-WIFI6-Touch-LCD-4B](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-4b.htm)
- **Display**: 4″ MIPI-DSI, 720×720
- **Touch**: GT911 (I²C)
- **Wi-Fi**: ESP32-C6 Coprocessor über SDIO

Konfig steckt komplett im Subordner [`waveshare-esp32-p4-wifi6-touch-lcd-4b/`](waveshare-esp32-p4-wifi6-touch-lcd-4b/).

## Funktionen

- **Dashboard** (Übersichtsseite): Kopfzeile mit großer Uhr + Datum, passive Statuszeile (Außen-/Innentemperatur, Luftfeuchte, PV-Erzeugung, Hausverbrauch, Akku-SoC), Mitteilungszeile, Stack-Widget mit Tab-Leiste (Heizung / Ventilator / Szenen) und drei Quick-Buttons.
- **Stack-Widget**: Heizung (Eve-Thermostat, ±0.5 °C, Ist/Ziel-Balken, Auto/Manuell/Aus), Ventilator (Xiaomi Standventilator: An/Aus, Geschwindigkeit, Schwenken, Preset, Sleep-Timer), Szenen.
- **Solar-PV-Visualisierung** auf der Energie-Seite — Plus-Layout mit Flussrichtungs-Pfeilen (Sonnenbatterie-Daten) und alternativer kompakter Listenansicht (Toggle oben rechts).
- **Detail-Pages** für Wetter (4-Slot-Stunden-Forecast), 3D-Drucker, Energie, Heizung, Staubsauger, Szenen und eine „Mehr"-Seite als Sammelpunkt.
- **Mitteilungen**: 2 Inline-Slots auf dem Dashboard (Icon, Text, Zeitstempel), Auto-Expire, Antippen verwirft. Per HA-Switch „Mitteilungen einklappen" verschwindet die Zeile, solange sie leer ist.
- **HA-Fernsteuerung**: Aktive Seite per Select wählen, Default-Seite konfigurieren, Auto-Revert nach Idle (Zeit einstellbar) / nach Display-Off ein- oder ausschalten — alles über HA-Entities exposed.
- **Tap-to-Wake** + Präsenz-Sensor-gesteuerter Bildschirmschoner.

## Setup

1. **ESPHome-Konfig in HA-Dashboard anlegen** mit Inhalt von [`waveshare-esp32-p4-wifi6-touch-lcd-4b/esphome.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/esphome.yaml). `name`, `friendly_name`, `room` in den Substitutions anpassen.
2. **`secrets.yaml`** im ESPHome-Dashboard pflegen — Variablen `wifi_ssid` und `wifi_password`.
3. **Wetter-Page-Forecast**: 12 Template-Sensoren in HA anlegen (Snippet im `template:`-Stil mit `weather.get_forecasts`-Trigger). Siehe Commit-Historie / Plan-Datei.
4. **Solar-Page**: erwartet die `sensor.sonnenbatterie_217105_state_*`-Entities aus dem Sonnenbatterie-HA-Plugin.
5. **Heizung**: `climate.eve_thermo_20ebp1701`. Bei abweichender Entity-ID die drei Stellen in `device/sensors.yaml` und die `homeassistant.service`-Blöcke im Heizung-Tab in `device/lvgl.yaml` anpassen.
6. **Ventilator**: `fan.dmaker_de_454884949_p33_s_2_fan` + `number.dmaker_de_454884949_p33_off_delay_time_p_3_1` (Sleep-Timer). Die Preset-Namen („Direkte brise" / „Natürliche brise") müssen exakt mit dem `preset_modes`-Attribut der Entity übereinstimmen.
7. **Luftfeuchte**: `sensor.eve_room_af92_humidity`.
8. Compilen & Flashen.

## Customizing

- **Theme-Farben/Fonts**: [`theme/button.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/theme/button.yaml) und [`assets/fonts.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/assets/fonts.yaml).
- **MDI-Glyphen**: [`assets/icons.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/assets/icons.yaml) — Substitutions-Map am Ende, Codepunkte aus [materialdesignicons.com/cdn/7.4.47/](https://materialdesignicons.com/cdn/7.4.47/).
- **Page-Layouts**: [`device/lvgl.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/device/lvgl.yaml).
- **HA-Bindings**: [`device/sensors.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/device/sensors.yaml).
- **HA-Controls** (Selects/Switches/Number/Text): [`addon/ha_controls.yaml`](waveshare-esp32-p4-wifi6-touch-lcd-4b/addon/ha_controls.yaml).

## Credits

Basis war [`jtenniswood/esphome-lvgl`](https://github.com/jtenniswood/esphome-lvgl) — die Grundkonfiguration der hardware stammen daher.
