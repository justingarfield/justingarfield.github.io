---
layout: base
---

# Software Links

| | | |
|-|-|-|
| TrinityCore/TrinityCore | https://github.com/TrinityCore/TrinityCore | |
| TrinityCore/WowPacketParser | https://github.com/TrinityCore/WowPacketParser | |
| nicolaw/trinitycore | https://gitlab.com/nicolaw/trinitycore | |
| | | |
| | | |


## Game Enhancing Addons

| Addon Name | Url | Description |
|-|-|-|
| CurseForge | https://download.curseforge.com/ | |
| wow-voiceover | https://github.com/mrthinger/wow-voiceover | |

## Development Related Addons

| Addon Name | Url | Description |
|-|-|-|
| | | |
| | | |
| | | |

## TCP Ports required for external clients

These ports are used during the initial Login process (_before the realmlist_)
* BattlenetPort: `TCP/1119`
* LoginREST.Port: `TCP/8081`

These ports are used _after_ you pick a Realm to connect to
* WorldServerPort: `TCP/8085`
* InstanceServerPort: `TCP/8086`

```
account set gmlevel 1#1 3 -1
account set gmlevel 2#1 3 -1
```

# References

* [wowdev/DBCD](https://github.com/wowdev/DBCD)
* [Kezan/Quests: Implement quest 14071 (Rolling with my homies)](https://github.com/TrinityCore/TrinityCore/pull/28253/commits/e4bd0c46af7536bc7249594bb1c5bfe6bdfede23)
* [Events](https://wowpedia.fandom.com/wiki/Events)
* [Hotfixes and how do they work](https://talk.trinitycore.org/t/hotfixes-and-how-do-they-work/31325)
* [WoWDatabaseEditor](https://github.com/BAndysc/WoWDatabaseEditor)
* [ymir wow packet sniffer](https://github.com/TrinityCore/ymir)
* [Steps to make a good sniff of an area](https://gist.github.com/Subv/c3498b5e952c5c978bcf)
* [wowdev/WoWDBDefs](https://github.com/wowdev/WoWDBDefs)
