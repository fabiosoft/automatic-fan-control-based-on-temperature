Hi, i really liked the work made by [Sam04](https://community.home-assistant.io/t/automatic-fan-control-based-on-presence-and-temperature/310398), so heavly inspired by his work i created my verion of this temperature controlled fan blueprint. 

Fan turns on if temperature inside is greater than the threshold value considering outside temperature (during winter). I removed the occupancy entity so the fan is dependent only from the temperature.

I use it to automatically turn on and off the cpu fan based on cpu temperature sensor, so it doesn't make useless noise.

Make sure to set the temperature device class for sensor and fan for entity even if you use a switch like me. (you can use platform template for both)

Sensors example:

Fan entity must be included in fan domain, if you have a switch, you shoud use a template.
```yaml
fan:
  - platform: template
    fans:
      cpu_rack_fan:
        friendly_name: "CPU Rack fan"
        value_template: "{{ 'on' if is_state('switch.fan_rack', 'on') else 'off' }}"
        turn_on:
          service: switch.turn_on
          data:
            entity_id: switch.fan_rack
        turn_off:
          service: switch.turn_off
          data:
            entity_id: switch.fan_rack
```

Temperature sensor -> template
```yaml
sensor:
  - platform: command_line
    name: CPU Temperature
    command: "cat /sys/class/hwmon/hwmon0/temp1_input"
    unit_of_measurement: "°C"
    value_template: '{{ value | multiply(0.001) | round(1) }}'
  - platform: template
    sensors:
      cpu_sensor_temperature:
        friendly_name: "CPU Temperature Sensor"
        unit_of_measurement: "°C"
        value_template: "{{ states('sensor.cpu_temperature') }}"
        device_class: temperature
```
