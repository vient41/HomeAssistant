# Network

## Not to forget
- [PFS Sense](https://www.youtube.com/watch?v=lUzSsX4T4WQ&ab_channel=NetworkChuck)
- [Pihole](https://www.youtube.com/watch?v=4X6KYN1cQ1Y&ab_channel=Goose)
- need ubiquitin Unifi because Virgin media box is shit and you can't disable DNS.... 

## IP Static

|  Hostname |  IP |   
|---|---|
|  switch Dell          | .3 |
| Imprimante Bureau     | .4 |
| Home Assistant rpi4   | .5 |
| PC Thomas             | .6 |
| AP Wifi 6             | .7 |
| Shield TV Pro         | .8 |
| NAS Synology          | .15 |





## Unifi Security Gateway and Access Points
The USG is controlled by a docker unify Network software hosted on Home Assistant. To manage USG and access points, we need to adopt them from the software. To ssh to a USG or AP when not adopted yet, we need to use default manufacturer credential (easy to find on internet), if the device is adopted already, then credential are stored in the advanced settings of the unify network software. When adopting, you must inform the USG or AP where to be paired. either by SSHing on the AP/USG and enter the command "set-inform http://<IPoftheUnifiNetworkSoft>:8080/inform" or search for word override and enter the IP of the Unify Network Software, it will automatically override the value when adopting new devices and constantly update already paired AP and USG.


Some configuration statements: 


- Virgin Media Box in Modem Mode
- USG in default configuration
- Dell Open Manage in default configuration
- credential to login the unify controller are stored in IBM 1password
- DHCP is hosted by USG


## Reverse proxy ?
I found possibility not to use reverse proxy [link](https://iximiuz.com/en/posts/multiple-containers-same-port-reverse-proxy/)
