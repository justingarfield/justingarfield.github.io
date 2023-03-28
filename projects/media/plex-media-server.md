# Plex Media Server Notes

## DNS Rebinding Issues

When running behind a firewall that protects against DNS Rebinding, you'll need to add a Whitelist entry for `plex.direct`, so that your local devices can resolve your Plex Media Server internally.

Note: Some folks have mentioned you may need to add `plex.direct` to Whitelist domains as well (in opnSense and others using Unbound DNS)

## GDM Discovery Traffic

By default, Plex enables GDM Device Discovery. If this stays enabled, be prepared to see broadcasts every 5-seconds via `UDP/32412` and `UDP/32414`

## References

* [Plex.direct - Unbound DNS - 21.7.1](https://forum.opnsense.org/index.php?topic=24349.0)
