## Home Assistant Templates
  
A collection of my Home Assistant templates and automations.  

#### Automations
The following automations can all be added in `Settings > Automations & Scenes`: 
* [Rain Live Activity](./automations/rain_live_activity.yaml) - This creates and displays a live activity on your iOS/Android device with the current rain rate of a rain sensor.  
* [Rain Stopped](./automations/rain_stopped.yaml) - This clears the live activity and sends a notification to advise the rain has stopped.  
* [Rain Notification](./automations/rain_notification.yaml) - This sends rain notifications to your movile device, with delays to stopp spamming whne rain is changable. The live activiy replaced this.
* [Sensor Offline](./automations/sensor_offline.yaml) - A basic notification to let me know when sensors go offline.
  
#### Combinations
The following are howtos with code for getting more complex sensors and dashboard cards up and running.  
* [Wet Bulb](./wet_bulb.md) - This sets up a wet bulb sensor and beautiful card to display on a dashboard (HT to [Viss](https://mastodon.social/@Viss) for the starting point for this.).  