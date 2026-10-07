## Home Assistant Templates
  
A collection of my Home Assistant templates and automations.  

#### Automations
The following automations can all be added in `Settings > Automations & Scenes`: 
#### [Rain Live Activity](./automations/rain_live_activity.yaml) - This creates and displays a live activity on your iOS/Android device with the current rain rate of a rain sensor.  
Useful infomraiton about the items in the sequence that trigger the live activity and update the dynamic island/live chip:  
This is the type of notification action to ensure it;s a live activity rather than a standard notification replace `iphone_17_pro_max` with the specific device name to alert:  
```yaml
- action: notify.mobile_app_iphone_17_pro_max
```
These items define it as a live update and provide a tag for the notification:  
```yaml
tag: raining_live
live_update: true
```
The items are what appears in the dynamic island, the icon and the data:  
```yaml
critical_text: "{{ states('sensor.ws3900a_rain_rate') }} mm/h"
notification_icon: mdi:weather-partly-rainy
```
#### [Rain Stopped](./automations/rain_stopped.yaml) - This clears the live activity and sends a notification to advise the rain has stopped.  
#### [Rain Notification](./automations/rain_notification.yaml) - This sends rain notifications to your movile device, with delays to stopp spamming whne rain is changable. The live activiy replaced this.
#### [Sensor Offline](./automations/sensor_offline.yaml) - A basic notification to let me know when sensors go offline.
  
#### Combinations
The following are howtos with code for getting more complex sensors and dashboard cards up and running.  
* [Wet Bulb](./wet_bulb.md) - This sets up a wet bulb sensor and beautiful card to display on a dashboard (HT to [Viss](https://mastodon.social/@Viss) for the starting point for this.).  