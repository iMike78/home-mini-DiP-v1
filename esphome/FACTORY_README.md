# MiciMike Factory Firmware Struktúra

## Áttekintés

A factory firmware struktúra lehetővé teszi, hogy:
1. Gyárilag minimális factory firmware legyen telepítve
2. Első indításkor BLE Improv-val WiFi beállítás
3. WiFi csatlakozás után automatikus frissítés a teljes firmware-re

## Fájlstruktúra

```
esphome/
├── micimike-factory.yml          # Factory firmware (gyári)
└── micimike-factory_full.yml     # Teljes firmware (a jelenlegi konfigod)
```

## Factory Firmware (micimike-factory.yml)

### Főbb komponensek:

1. **Package import**: Betölti a teljes firmware-t
   ```yaml
   packages:
     micimike_full: !include micimike-factory_full.yml
   ```

2. **BLE Improv provisioning**: 
   - Touch center gomb használata authentikációhoz
   - BLE kapcsolódás WiFi beállításhoz
   
3. **HTTP OTA frissítés**:
   - GitHub releases-ről tölti le a manifest-et
   - Automatikusan frissít a teljes firmware-re

4. **Beta firmware switch**:
   - Lehetővé teszi beta verzió használatát
   - Manifest URL váltás production/beta között

## Kulcsfontosságú különbségek a HA VPE-hez képest:

### 1. Center Button Authorizer
HA VPE: `authorizer: center_button` (fizikai gomb)
MiciMike: `authorizer: center_button` (touch gomb - ESP32-S3 GPIO3)

A touch gomb ugyanúgy működik, mint a fizikai:
```yaml
binary_sensor:
  - platform: esp32_touch
    id: center_button
    threshold: 5000
    pin: GPIO3
```

### 2. LED vezérlés
A factory firmware során is működik a `control_leds` script, ami:
- BLE provisioning alatt: `improv_ble_in_progress = true`
- Inicializálás alatt: `init_in_progress = true`
- WiFi csatlakozás után: normál LED működés

## Telepítési folyamat

### 1. Factory firmware égetése (USB)
```bash
esphome run micimike-factory.yml
```

### 2. BLE Improv provisioning
- Eszköz AP módban indul
- ESPHome app vagy Home Assistant felderíti
- Touch center gomb megnyomásával authentikáció
- WiFi beállítás

### 3. Automatikus frissítés
- WiFi csatlakozás után 5 sec delay
- BLE kikapcsolás
- HTTP OTA letölti a manifest-et
- Automatikus frissítés a teljes firmware-re
- Újraindulás után teljes funkcionalitás

## Manifest.json struktúra

A GitHub releases-ben kell lennie egy manifest.json-nak:

```json
{
  "name": "MiciMike Voice Assistant",
  "version": "v0.9.0",
  "builds": [
    {
      "chipFamily": "ESP32-S3",
      "improv": false,
      "parts": [
        {
          "path": "micimike-factory_full.bin",
          "offset": 0
        }
      ]
    }
  ]
}
```

## GitHub Releases struktúra

```
Releases/
├── v0.9.0/
│   ├── manifest.json           # Production manifest
│   ├── manifest-beta.json      # Beta manifest
│   └── micimike-factory_full.bin  # Teljes firmware binary
```

## Előnyök

1. **Egyszerű gyári telepítés**: Csak a factory firmware-t kell égetni
2. **OTA frissítés**: Felhasználók WiFi-n keresztül kapják a teljes verziót
3. **Beta csatorna**: Opcionális beta firmware tesztelés
4. **Nincs USB függőség**: Első telepítés után minden OTA-n keresztül
5. **Verziókövetés**: GitHub releases alapú verziókezelés

## Megjegyzések

- A touch center gomb ugyanúgy működik authentikációként, mint a fizikai
- A teljes konfiguráció minden funkciója megmarad
- A factory firmware csak a bootstrap funkciókat tartalmazza
- A LED vezérlés mindkét verzióban működik
