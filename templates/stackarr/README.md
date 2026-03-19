# Stackarr

Stackarr is a collection containers to provide basically everything you need from the *arr stack!
It is easily scalable, as it is as easy as just adding the services to the docker compose file and redeploying it.

## Overview

Stackarr has the following services by default in it:
- [Flaresolverr](https://github.com/FlareSolverr/FlareSolverr)
- [Prowlarr](https://prowlarr.com/)
- [Sonarr](https://sonarr.tv/)
- [Radarr](https://radarr.video/)
- [Jellyfin](https://jellyfin.org/)
- [Seerr](https://docs.seerr.dev/)
- [Deluge](https://deluge-torrent.org/)

If you want, you can add or remove the services as you wish.
For example you can add [Lidarr](https://lidarr.audio/), you can also change Deluge for [qbittorrent](https://www.qbittorrent.org/) or [Transmission](https://transmissionbt.com/).

For more you can check out this curated list of *arr for more options: https://github.com/Ravencentric/awesome-arr

## Folder structures

The folder structure is very easy to manage:

```
stackarr
├── config
│   ├── prowlarr
│   ├── sonarr
│   ├── radarr
│   ├── jellyfin
│   ├── seerr
│   └── deluge
├── media
│   ├── TV Series
│   ├── Anime
│   └── Movies
└── downloads
```

It is recommended to replace all the dynamic folders with an absolute path.
You can simply open the docker compose file with any text editor find and replace all the `./` characters.

## Trouble shooting

Make sure that all the folders are owned by you (and not by `root`) on the server.
Else containers like Jellyfin or Seerr won't be able to write the folders.

## Guide for Stackarr

### Summary

This is a guide on how to use the services for Stackarr.
Flaresolverr isn't configured as it doesn't really need configuration.

- [Deluge](#deluge)
- [Prowlarr]()
- [Sonarr]()
- [Radarr]()
- [Jellyfin]()
- [Seerr]()
- [Cleanuparr]()

### Deluge

Deluge is a lightweight BitTorrent client. The main tool for which you will use to download stuff from the internet.

You can access Deluge by browsing to http://your.server.ip:8112 on your browser.
You will be prompted for a password, like below:

![deluge first login password prompt image](../../assets/deluge-first-login.png)

NOTE: The WebUI won't be of the same color on first login, as this the `access` theme.

The default password of the docker container is `deluge`.
Afterwards, you will be prompted to change the password, make sure it is a secure one.
You can always change password inside by going into `preferences` (on the top bar) -> `interface` tab -> WebUI Password.

Here you can also change the theme of the WebUI if you don't want light mode.

You will also need to enable the `label` plugin for Cleanuparr later, you can do so by going into the `plugin` tab (under `preferences`), and enabling the `label` plugin.

If you don't see the label plugin, or any plugins, then it is recommended to rollback the version of deluge.
You can do so by modifying the docker compose and instead of using `deluge:latest`, you can use `deluge:2.2.0-r1-ls364` (`deluge:arm64v8-2.2.0-r1-ls364` for ARM cpus).

Once you rebuild the container you should see the plugins available, simply check the `Label` plugin like below:

<img src="../../assets/deluge-plugins.png" width=50% height=50% alt="deluge plugin manager checked">
