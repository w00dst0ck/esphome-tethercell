# esphome-tethercell
After having a few Tethercell batteries lying unused in a drawer for a few years, it was time to put them back to use.

Integration into Home Assistant is possible via ESPHome.

Example esphome configuration as package to switch a tethercell battery via bluetooth

```yaml
esp32_ble_tracker:

packages:
  tethercell1:
    url: https://github.com/w00dst0ck/esphome-tethercell
    files:
      - path: esphome_tethercell_generic.yaml
        vars:
          tethercell_number: 1
          tethercell_mac: 88:33:14:49:XX:XX
          tethercell_pin: 00000000
    ref: main
    refresh: 1d

  tethercell2:
    url: https://github.com/w00dst0ck/esphome-tethercell
    files:
      - path: esphome_tethercell_generic.yaml
        vars:
          tethercell_number: 2
          tethercell_mac: 88:33:14:49:XX:XX
          tethercell_pin: 00000000
    ref: main
    refresh: 1d
```
