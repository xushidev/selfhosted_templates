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