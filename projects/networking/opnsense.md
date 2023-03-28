# Unbound DNS

## OOTB Blocklists

These are the following OOTB blocklists that I use at home:

| Name | Description |
|-|-|
| [Abuse.ch](https://threatfox.abuse.ch/) - ThreatFox IOC database |  |
| [AdAway List]() |  |
| [AdGuard List]() |  |
| [Simple Ad LIst]() |  |
| [Simple Tracker List]() |  |
| [Steven Black List]() | Same source that Pi-hole uses OOTB |
| [YoYo List](https://pgl.yoyo.org/adservers/) |  |

Note: The actual blocklist feed URLs that OPNsense is using for all of these can be found at https://github.com/opnsense/core/blob/23.1.3/src/opnsense/service/templates/OPNsense/Unbound/core/blocklists.conf

Maybe 
| [youTube_ads_4_pi-hole](https://github.com/kboghdady/youTube_ads_4_pi-hole/) |  |


## C-ICAP and ClamAV

https://docs.opnsense.org/manual/how-tos/proxyicapantivirusinternal.html

* Web Proxy
  * Administration
    * Forward Proxy
      * General Forward Settings
        * Assign interfaces
        * Apply
      * Access Control List
        * Allowed SSL ports
          * Added `32400:plex`
      * ICAP Settings
        * Enable ICAP
        
    * General Proxy Settings
      * Enable proxy
      * Apply
    * Local Cache Settings
      * Enable local cache
      * Apply
      * Restart Service
    

* Install c-icap and clamav plugins under Firmware
* Refresh to see new services in menu

* ClamAV
  * General
    * Enable clamd service
    * Enable freshclam service
    * Apply
  * Signatures
    * **Download signatures** (takes about 2-mins)
  * Versions
    * Refresh until signatures show up

* C-ICAP
  * Antivirus
    * Enable ClamAV
    * Apply
  * General
    * Set `Server admin`
    * Set `Servername`
    * Enable c-icap service
    * Apply
