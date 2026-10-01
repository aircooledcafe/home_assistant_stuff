## Wet Bulb Sensor and Dashboard Card
  
This is made of two components. a sensor to calculate the wet bulp metrics from your temperature and humidity sensors.
A tamplate to display the data in a pleasing card within a Home Assistant dashboard.  

The sensor, create this in:  
`Settings > Devices & services > Helpers > + Create helper > Templates > Sensor`  
  
Paste the following into the "State*" field:  
```jinja
{% set temp_entity = 'sensor.ws3900a_outdoor_temperature' %}
{% set rh_entity = 'sensor.ws3900a_humidity' %}

{% set temp = states(temp_entity) | float(none) %}
{% set rh = states(rh_entity) | float(none) %}

{% if temp is none or rh is none %}
unknown
{% else %}

{# delete either 1 or 2 as you desire #}
{# 1 calculating the wet bulb global temperature using Stull's equation #}
{% set wbt = temp * atan(0.151977 * ((rh + 8.313659) ** 0.5)) 
            + atan(temp + rh) 
            - atan(rh - 1.676331) 
            + (0.00391838 * (rh ** 1.5) * atan(0.023101 * rh)) 
            - 4.686035 %}
{% set wbgt = (0.7 * wbt) + (0.3 * temp) %}
{# end 1 #}

{{ wbgt | round(1) if wbgt is not none else 'unknown' }}
{% endif %}
```
Give it an appropriate title, e.g. "Wet Bulb Garden", and save it.  
  
The card for displaying on your dashboard requires several additional items to be installed on your home assitant server, these can be installed using [HACS](https://hacs.xyz/).  
Just search for the folllwing in HACS once it's installed:  
[ApexCharts Card](https://github.com/RomRider/apexcharts-card) A tool that provides additional charting options to the lovelace UI.   
[Mushroom](https://github.com/piitaya/lovelace-mushroom) A tool which provides nicley laid out cards and some good customisation options.  
  
Here is the full template code, you will need to replace the sensors with your sepcific senor IDs. This will need to be pasted into a new card item on whichever dahsbord you choose:    
```yaml
type: vertical-stack
cards:
  - type: heading
    heading: Wet Bulb Risk Outside
    heading_style: title
    icon: mdi:white-balance-sunny
    grid_options:
      columns: 12
      rows: 1
  - type: custom:mushroom-template-card
    entity: sensor.wet_bulb_garden
    primary: |
      {{ states('sensor.wet_bulb_garden') }}°C WBGT
    secondary: >
      {% set w = states('sensor.wet_bulb_garden') | float(none) %} {% set t =
      states('sensor.ws3900a_outdoor_temperature') %} {% set h =
      states('sensor.ws3900a_humidity') %} {% if w is none %}
        Unavailable · Temp {{ t }}°C · RH {{ h }}%
      {% elif w < 25 %}
        Blue · Low · Temp {{ t }}°C · RH {{ h }}%
      {% elif w < 27.8 %}
        Green · Elevated · Temp {{ t }}°C · RH {{ h }}%
      {% elif w < 29.5  %}
        Yellow · Warning · Temp {{ t }}°C · RH {{ h }}%
      {% elif w < 31.8 %}
        Red · Dangerous · Temp {{ t }}°C · RH {{ h }}%
      {% else %}
        Black · Extreme · Temp {{ t }}°F · RH {{ h }}%
      {% endif %}
    icon: mdi:sun-thermometer
    icon_color: >
      {% set w = states('sensor.wet_bulb_garden') | float(none) %} {% if w is
      none %}
        grey
      {% elif w < 25 %}
        blue
      {% elif w < 27.8 %}
        green
      {% elif w < 29.5 %}
        amber
      {% elif w < 31.8 %}
        red
      {% else %}
        white
      {% endif %}
    badge_icon: >
      {% set w = states('sensor.wet_bulb_garden') | float(none) %} {% if w is
      none %}
        mdi:help
      {% elif w < 27.8 %}
        mdi:check
      {% elif w < 29.5 %}
        mdi:alert
      {% elif w < 31.8 %}
        mdi:fire-alert
      {% else %}
        mdi:skull-crossbones
      {% endif %}
    badge_color: >
      {% set w = states('sensor.wet_bulb_garden') | float(none) %} {% if w is
      none %}
        grey
      {% elif w < 27.8 %}
        green
      {% elif w < 29.5 %}
        amber
      {% elif w < 31.8 %}
        red
      {% else %}
        black
      {% endif %}
    layout: horizontal
    tap_action:
      action: more-info
  - type: gauge
    entity: sensor.wet_bulb_garden
    name: Wet Bulb Risk
    min: 12
    max: 37.8
    needle: true
    segments:
      - from: 12
        color: '#039be5'
      - from: 25
        color: '#43a047'
      - from: 27.8
        color: '#ffb300'
      - from: 29.8
        color: '#e53935'
      - from: 31.8
        color: '#000000'
  - type: custom:apexcharts-card
    experimental:
      color_threshold: true
    graph_span: 12h
    header:
      show: true
      title: Wet Bulb history
      show_states: true
      colorize_states: true
    yaxis:
      - min: 21
        max: 37.8
        decimals: 1
    apex_config:
      chart:
        height: 220
      legend:
        show: false
      annotations:
        yaxis:
          - 'y': 21
            y2: 25
            fillColor: '#039be5'
            opacity: 0.1
          - 'y': 25
            y2: 27.8
            fillColor: '#43a047'
            opacity: 0.1
          - 'y': 27.8
            y2: 29.5
            fillColor: '#ffb300'
            opacity: 0.14
          - 'y': 29.5
            y2: 31.7
            fillColor: '#e53935'
            opacity: 0.13
          - 'y': 31.67
            y2: 37.8
            fillColor: '#000000'
            opacity: 0.15
    series:
      - entity: sensor.wet_bulb_garden
        name: WBGT
        type: area
        stroke_width: 3
        unit: °C
        color_threshold:
          - value: 21.1
            color: '#039be5'
          - value: 25
            color: '#43a047'
          - value: 27.8
            color: '#ffb300'
          - value: 29.5
            color: '#e53935'
          - value: 31.8
            color: '#eeeeee'
        group_by:
          duration: 5min
          func: avg
```
  