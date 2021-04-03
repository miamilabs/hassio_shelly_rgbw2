# Shelly RGBW2 on HASS.IO
Shelly MQTT Scripts for Shelly RGBW2

With this code block you can use RGB and the White strip as seperate Channels.
By  default you will turn on the RGB or White strip with low Brigtness.

```
light:
  - platform: mqtt
    schema: template
    name: "LED WHITE"
    command_topic: "shellies/shellyrgbw2-XXXXX/color/0/set"
    state_topic: "shellies/shellyrgbw2-XXXXX/color/0/status"
    command_on_template: >
      {"turn": "on", "gain": 0, "red": 0, "green": 0, "blue": 0
      {%- if brightness is defined -%}
      , "white": {{ brightness | float |  round(0) }}
      {% else %}
      , "white": 10
      {%- endif -%}
      , "effect": 0}
    command_off_template: '{"turn":"off"}'
    state_template: "{% if value_json.ison and value_json.white > 0 and value_json.red == 0 and value_json.green == 0 and value_json.blue == 0 %}on{% else %}off{% endif %}"
    white_value_template: "{{ value_json.gain | float  | round(0) }}"
    brightness_template: '{{value_json.white}}'
    qos: 2 
    
  - platform: mqtt
    schema: template
    name: "LED RGB"
    command_topic: "shellies/shellyrgbw2-XXXXX/color/0/set"
    state_topic: "shellies/shellyrgbw2-XXXXX/color/0/status"
    effect_list:
      - 0
      - 1
      - 2
      - 3
      - 4
      - 5
      - 6
    command_on_template: >
      {"turn": "on"
      {%- if brightness is defined -%}
      , "gain": {{brightness | float | multiply(0.3922) | round(0)}}
      {% else  %}
          {% if red is defined or green is defined or blue is defined %}
            {%- if red is defined -%}
                , "red": {{ red }}
            {%- endif -%}
            {%- if green is defined -%}
                , "green": {{ green }}
            {%- endif -%}
            {%- if blue is defined -%}
                , "blue": {{ blue }}
            {%- endif -%}
          {% else %}
          , "gain": 5
          , "red": 10, "green": 10, "blue": 10
          {%- endif -%}
      {%- endif -%}
      , "white": 0
      
      {%- if effect is defined -%}
      , "effect": {{ effect }}
      {%- endif -%}
      }
    command_off_template: '{"turn":"off"}'
    state_template: "{% if value_json.ison and value_json.white <= 0 %}on{% else %}off{% endif %}"
    brightness_template: "{{ value_json.gain | float | multiply(2.55) | round(0) }}"
    red_template: '{{ value_json.red }}'
    green_template: '{{ value_json.green }}'
    blue_template: '{{ value_json.blue }}'
    white_value_template: '{{ value_json.white }}'
    effect_template: '{{ value_json.effect }}'
    qos: 2
```
