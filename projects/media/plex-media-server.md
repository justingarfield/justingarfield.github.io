# Plex Media Server Notes

I've been using Plex Media Server (PMS) since it originally came out. I've used it on Windows, Linux, in Docker, and containerd without issue.

This is my collection of information over the years, as well as how I utilize it.

## DNS Rebinding Issues

When running behind a firewall that protects against DNS Rebinding, you'll need to add a Whitelist entry for `plex.direct`, so that your local devices can resolve your Plex Media Server internally.

Note: Some folks have mentioned you may need to add `plex.direct` to Whitelist domains as well (in opnSense and others using Unbound DNS)

## GDM Discovery Traffic

By default, Plex enables GDM Device Discovery. If this stays enabled, be prepared to see broadcasts every 5-seconds via `UDP/32412` and `UDP/32414`

## Hardware Sizing

* [Plex Media Server Hardware Transcoding Cheat Sheet](https://www.elpamsoft.com/?p=Plex-Hardware-Transcoding)

## References

### Related to Projects

* [Plex.direct - Unbound DNS - 21.7.1](https://forum.opnsense.org/index.php?topic=24349.0)
* [reddit: NVIDIA Quadro P2000](https://www.reddit.com/r/PleX/comments/qgb49f/nvidia_quadro_p2000/)
* [TRaSH Guides](https://trash-guides.info/)
* [HOW PLEX IS DOING HTTPS FOR ALL ITS USERS](https://words.filippo.io/how-plex-is-doing-https-for-all-its-users/)
* [How to Use Secure Server Connections](https://support.plex.tv/articles/206225077-how-to-use-secure-server-connections/)
* [Restore a Database Backed Up via ‘Scheduled Tasks’](https://support.plex.tv/articles/202485658-restore-a-database-backed-up-via-scheduled-tasks/)
* [Advanced, Hidden Server Settings](https://support.plex.tv/articles/201105343-advanced-hidden-server-settings/)
* [Build your very own self-hosting platform with Raspberry Pi and Kubernetes](https://kauri.io/#build-your-very-own-self-hosting-platform-with-raspberry-pi-and-kubernetes-introduction/1229f21044ef4bff8df35875d6803776/a)

### Related to API / Development

* [Plex Media Server URL Commands](https://support.plex.tv/articles/201638786-plex-media-server-url-commands/)
* [Finding an authentication token / X-Plex-Token](https://support.plex.tv/articles/204059436-finding-an-authentication-token-x-plex-token/)
* [Arcanemagus/plex-api](https://github.com/Arcanemagus/plex-api/wiki)
* [phillipj/node-plex-api](https://github.com/phillipj/node-plex-api)
* [pkkid/python-plexapi](https://github.com/pkkid/python-plexapi)
* [python-plexapi](https://python-plexapi.readthedocs.io/en/latest/index.html)
* [npm - plex-api](https://www.npmjs.com/package/plex-api)
* [PMS web api documentation](https://forums.plex.tv/t/pms-web-api-documentation/62152)

## Related to Integrations / 3rd Party

* [tgorgdotcom/locast2plex: Error in function login: Forbidden](https://github.com/tgorgdotcom/locast2plex/issues/272)
* [SpaceK33z/plex2netflix](https://github.com/SpaceK33z/plex2netflix)
* [Tautulli/Tautulli](https://github.com/Tautulli/Tautulli)
