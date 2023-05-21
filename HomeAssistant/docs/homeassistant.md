
# Home Assistant

## Entity naming convention
- ROOM_DEVICES_POSITON-IN-THE-ROOM

# Duckdns and portforward

Follow this video to get duck DNS and port forward installation. This article is also usefull for port fowarding on USG : [link](https://www.appuntidallarete.com/ubiquiti-unifi-usg-udm-port-forwarding-configuration-and-troubleshooting/)

![portforwad](images/portforward1.PNG)
![portforwad](images/portforward2.PNG)
![portforwad](images/portforward3.PNG)

# Smoke and CO detectors
Model fireangel SB1 optical smoke alarm.  

Test : press 5 second the test button and it will bip 2 times.
When biping for alarm smoke presence, press test for 5 second, it will silence the alarm for 8 min. [Manual](https://www.google.com/url?sa=t&source=web&rct=j&url=https://www.safelincs.co.uk/templates_safelincs/files/datasheets/3659_FireAngel%2520FASB1R%2520User%2520Manual.pdf&ved=2ahUKEwiIs5-nhJ_9AhXUhFwKHf43AX0QFnoECB8QAQ&usg=AOvVaw3HvTiUHkAaTbzhFzzZUQTF)


CO detector model fireangel FA3313.

There is a possibility to deactivate it with a small spin a keep stored in the office draw. 
To test, push the button test, it will ignite bips (2 times after I tested it). 
CO light is red when alarm detected high CO value.
The exclamation point means battery low and the puls wave is the heartbeat if working and blink green. 
Test every month and requires an automation on home assistant. [Manual](https://www.google.com/amp/s/manuals.plus/fireangle/fa3313-carbon-monoxide-alarms-manual/amp)

# Security Cameras
- double take combine NVR and face recognition
- deepstack for recognition

## Hikvision integration
After few tries with Frigate I reliazed that the motion detection as well as the stream were not as good as expected. So I moved to hikvision integration for testing where I've been able to stream an image at 15fps (not confirmed, said by the tool) and able to get motion detection made by Annke camera embedded software and an entity on HA to detect the motion. 

1. configure Annke camera event ![camera1](images/annke1.PNG)
2. configure Annke camera system ![camera2](images/annke2.PNG)
3. on HA, install Hikvision integration modifying the config.yaml file ![camera3](images/annke3.PNG)
4. restart HA and the camera should be visible as an entity. 
5. to create an automation, this was working ![camera4](images/annke4.PNG) ![camera5](images/annke5.PNG) ![camera6](images/annke6.PNG)

## Frigate
[Installation](https://youtu.be/_61-hIL1AjQ)
```
mqtt:
  host: 192.168.x.x
  user: xxx
  password: xxx

cameras:
  Driveway: # <------ Name the camera
    ffmpeg:
      inputs:
        - path: rtsp://xxx:mdp@192.168.x.x:554/H264/ch1/sub/av_stream # <----- Update for your camera
          roles:
            - detect
            - rtmp
            - record
    record:
      enabled: True
      retain:
        days: 2
        mode: motion
    rtmp:
      enabled: False # <-- RTMP should be disabled if your stream is not H264
    detect:
      width: 640  # <---- update for your camera's resolution
      height: 480 # <---- update for your camera's resolution
      fps: 5
```
I needed to move to sub stream because 4K camera stream is too big for the rpi. it's does work well even with cheap camera... May not need to buy high end next time.

# Backup
Once the installation of HASSio is done, must put in place backup system. 

Back up and restore Home Assistant


Backup of your Home Assistant and add-on data and configuration. They are stored in a compressed archive file (.tar). Backups are made from the Supervisor Backups panel. There is also a service available that allows you to trigger the creation of a backup from an automation. Backups are stored in the /backup directory.
A full backup includes the following directories:

- config
- share
- addons (only manually installed or created add-ons, not those installed from the store)
- ssl
- media


A partial backup consists of any number of the above default directories and installed add-ons.

## Backup from the UI
1. Go to Configuration > Add-ons & Backups > Backups in the UI.
2. Provide a name for the backup.
3. Choose full or partial.
4. Choose to password protect or not. Password-protected backups cannot easily be browsed outside of Home Assistant OS.
5. Click “Create” to begin the backup.

## Restoring backup after new install
You can make use of backup which you have copied off of a previous install to restore to a new installation during the onboarding process. Follow the link at the bottom of the account creation page to upload your backup from the previous installation.
For restoring a backup at any other time, visit the Supervisor backup panel in your UI and use the following steps:

- Select “Upload Backup” from the icon in the upper right of the page.
- Click on the folder icon to navigate to your backup .tar file and select it.
- When the upload is completed, you will be presented with the backup restore dialog for restoring it, and can then choose to restore in full or in part by manually selecting individual items.
- If the backup you are uploading is more than 1GB in size, it can be faster and more efficient to make use of the Samba add-on in order to transfer files to the /backup directory.
- The length of time it takes to create or restore backup will depend on how much you have to compress or decompress.
- If you’re looking to slim down your backup, check if your configuration directory contains a large database file (home-assistant_v2.db). See the recorder integration page for options to keep your database data down to a size that won’t cause issues. Note the keep days, purge interval, and include/exclude options.
- When the restore is complete, Home Assistant will restart to apply the new settings. You will lose the connection to the UI and it will return once the restart is completed.
- Can also use the CLI commande to do so. 

## Copy to another distant location

Copy the back to Sysnology NAS via SMB share. Very easy to do. 
[Link](https://github.com/thomasmauerer/hassio-addons/tree/master/samba-backup)

The end of this video shows how to add a back up entity on the Home assistant dashboard: [Link](https://www.youtube.com/watch?v=bY39OpoN8OM&t=956s)

```
Configuration that worked:
host: 192.168.0.20
share: Sauvegarde_distante
target_dir: /HomeAssistant
username: admin
password: mdpnormal
keep_local: 0
keep_remote: 2
trigger_time: '00:00'
trigger_days:
- Mon
- Tue
- Wed
- Thu
- Fri
- Sat
- Sun
exclude_addons: []
exclude_folders: []
```

```
Logs:
[s6-init] making user provided files available at /var/run/s6/etc...exited 0.
[s6-init] ensuring user provided files have correct perms...exited 0.
[fix-attrs.d] applying ownership & permissions fixes...
[fix-attrs.d] done.
[cont-init.d] executing container initialization scripts...
[cont-init.d] done.
[services.d] starting services
[services.d] done.
[22-03-14 18:15:43] INFO: ---------------------------------------------------
[22-03-14 18:15:43] INFO: Host/Share: 192.168.0.20/Sauvegarde_distante
[22-03-14 18:15:43] INFO: Target directory: /HomeAssistant
[22-03-14 18:15:43] INFO: Keep local/remote: 0/2
[22-03-14 18:15:43] INFO: Trigger time: 18:20
[22-03-14 18:15:43] INFO: Trigger days: Mon Tue Wed Thu Fri Sat Sun
[22-03-14 18:15:43] INFO: ---------------------------------------------------
[22-03-14 18:15:46] INFO: Samba Backup started successfully
[22-03-14 18:20:46] INFO: Backup running ...
[22-03-14 18:20:46] INFO: Creating backup "Samba Backup 2022-03-14 18:20"
[22-03-14 18:20:48] INFO: Copying backup d4aec165 (Samba_Backup_2022_03_14_18_20.tar) to share
[22-03-14 18:20:49] INFO: Deleting d4aec165 (Samba Backup 2022-03-14 18:20) local
[22-03-14 18:21:02] INFO: Backup finished
``` 


## create a manual back-up 

We need to create a script and execute it. 

service: hassio.addon_stdin
data:
addon: 15d21743_samba_backup
input: trigger

![New script](./images/backupscript.PNG)


## HACS Home Assistant Community Store

[Link](https://hacs.xyz/)
This is a library of github repositories for bakend integration and front end view (themes....). I found usefull :
Front end : 


- Tracking Number Card for Home Assistant
- scheduler-card
- Travel Time Card for Home Assistant
 
Integrations :
 
- Car Wash
- Amazon Rekognition
- Office 365 Integration for Home Assistant
- Nespresso Ble coffee machine


## Zigbee Home Assistant (ZHA)

Plugin the Sonoff Zigbee dongle on the Home Assistant and restart the Home assistant. By doing that I have automatically discovered the dongle into Home Assistant. 

![ZHA1](./images/zha1.PNG)

Once clicked on Configure there is a step to automatically install the dongle. This is a successful install : 

![ZHA2](./images/zha2.PNG)

[Link](https://sonoff.tech/product-review/sonoff-zigbee-3-0-usb-dongle-plus-tutorials/) give us the next step to add devices.

 Add sub-devices to the gateway dongle

![ZHA3](./images/zha3.png)
![ZHA4](./images/zha4.png)

Make the sub-devices enter the pairing mode, and you can control it after adding successfully

![ZHA5](./images/zha5.png)


## Mirror

- [Link1](https://youtu.be/RM3Xvo08t10)
- [Link2](https://magicmirror.builders/)
- [Link3](https://docs.magicmirror.builders/getting-started/installation.html#manual-installation)

Automatic Installation Scripts
Sam (@sdetweil, long time contributor of the MagicMirror² framework) maintains a easy to use installation and update script: [Link](https://github.com/sdetweil/MagicMirror_scripts)


The MagicMirror Package Manager is a command line interface designed to simplify the installation, removal, and maintenance of MagicMirror modules


## Roborock and Xiaomi

Roboroc integration doesn't work for now and automatically ask to make use of xiaomi Miio integration instead. The roborock needs to be controlled from the Mobile application "Mi Home" instead of the Roborock application. It doesn't change anything in the interface, once logged in, the interface is exactly the same. The following integration appears once found by Xiaomi home assistant integration tool. 

![roborock](./images/roborock.PNG)

I found a [HACS map extractor](https://havient.duckdns.org/hacs/repository/299753146) to make a beautiful dashboard with the map of the vacuum. I don't feel like it is a need for now. It's called "Xiaomi Cloud Map Extractor".

## My feedback and questions:
- je ne comprends pas comment imbriquer les briques d'automation les unes aux autres.
- Je veux créer des briques d'automations élémentaires pour les cascader et arriver à un modèle complexe
- Sous quel forme se présente ces briques élémentaire dans Home Assistant ? 
- Node red offre-t-il un affichage "human readable" que Home assistant n'offre pas ?
- Est-e que HA sait déclencher des automations sur des evenements ? 
- Fonctionement en machine d'état posible ?? 