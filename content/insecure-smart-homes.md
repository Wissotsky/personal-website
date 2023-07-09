+++

title = 'Authentication Gap in ESPHome'

+++
> Preable: I have contacted the ESPHome developers with my findings a while ago, they were not interested in changing the lack of authentication by default.

If youre reading this post its likely you have experimented with smarthome and iot stuff, or have at the very least thought about. Its also just as likely you care about the safety of your own home.

[Smarthome minigame]

Despite that many smart home enthusiasts left their homes exposed to the internet, because authentication is disabled by default in ESPHome.

[ESPHome](https://esphome.io/index.html) is a platform that DIY enthusiasts often use for their smart home projects. It allows you to use common microcontrollers like ESP32 to make your devices "smart".
Additionally it makes managing configuration, versioning, controlling and automating devices much easier.

## MITRE ATT&CK

| Name | ID |
|---|---|
| Active Scanning: Scanning IP Blocks | [T1595.001](https://attack.mitre.org/techniques/T1595/001/) |
| Search Open Technical Databases: Digital Certificates | [T1596.003](https://attack.mitre.org/techniques/T1596/003/) |
| Exploit Public-Facing Application | [T0819](https://attack.mitre.org/techniques/T0819/) |
| Program Upload | [T0845](https://attack.mitre.org/techniques/T0845/) |
| System Firmware | [T0857](https://attack.mitre.org/techniques/T0857/) |
| Manipulation of Control | [T0831](https://attack.mitre.org/techniques/T0831/) |

## Data leaks

ESPHome users can expose two types of interfaces, a custom made interface by the user to control one device(Web Server) and the admin dashboard where devices and configurations are managed.

### Web Server leaks

- Logs: The device logs can themselves contain sensitive information and can be monitored to infer behavior like occupancy times.
- Controls: You can use controls like temperature and lights that were set up by the user.
- OTA: You can upload your own firmware to the device.

### Admin Dashboard leaks

- Device configurations and secrets
- Dump last uploaded OTA for device
- Upload firmware OTA
- Create new devices

## Finding ESPHome instances

You can find ESPHome instances via both passive and active scanning.

### Fingerprints(Active)

Admin dashboard:

- iconhash:"2655a07340b885b35aae4244891d6ee4"
- title:Dashboard - ESPHome

User Dashboard:

- "https://oi.esphome.io/v2/www.js"

MQTT:

- esphome +port:"1883" +service:"mqtt"

### Domains(Passive)

If you have the infrastructure to monitor the letsencrypt certlog you can look out for these subdomains

- esp.example.com
- esphome.example.com

## Abuse potential

Imagine if during a ransomware attack the attackers took control over air conditioning and made the office unbearable by overheating or overcooling it. While it may seem insignificant in the grand scheme of things. In the moment the mental impact can be tremendous, potentially overshadowing the future organizational consequences of the ransomware attack itself during Incident Response

[Fire dog meme]

### Low skill

- Trolling
- Electric bill attacks

Most attacks you will see are likely opportunistic script kiddies messing with lights, ac temperature and lawn sprinklers.

For the more long term attackers you could see them turning on pool heating or maxing out the ac at night to attack the financials of the org.

### High skill

- Goldmine for targeted attacks
- Incredibly hard to scale, likely unprofitable

This is useless on its own for financially motivated actors, but as mentioned above can be devastating in combination with other attacks.

## Patching

For the admin dashboard you can add a username and password through the launch command
<https://esphome.io/guides/cli.html#dashboard-command>

For the web server you can add a username and password to the config(The config could is exposed by the dashboard, so make sure its secured first)
<https://esphome.io/components/web_server.html#configuration-variables>

Follow general IoT security practices such as:

- Network Isolation
- Security updates
- Tamper proof hardware when necessary
- Network encryption
