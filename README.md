# lidarr-on-ultra-steroids
A maintained fork of [lidarr-on-steroids](https://github.com/youegraillot/lidarr-on-steroids) with updated images and automatic weekly rebuilds.
[![Build and Push](https://github.com/Crapcraft/lidarr-on-ultra-steroids/actions/workflows/build.yml/badge.svg)](https://github.com/Crapcraft/lidarr-on-ultra-steroids/actions/workflows/build.yml)


## What's different from the original
- Base image updated from `hotio/lidarr:pr-plugins` (dead) to `hotio/lidarr:nightly` (active, plugin support included)
- Node.js updated from `node:21-alpine` (EOL) to `node:lts-alpine` (current LTS, auto-tracking)
- GitHub Actions workflow rebuilds and pushes to GHCR automatically every Monday

## Important note
This image uses `hotio/lidarr:nightly` which includes plugin support. Once you run this image you cannot revert to a standard Lidarr branch without restoring a database backup from before switching.

## Usage
### Docker Compose
    services:
      lidarr:
        image: ghcr.io/crapcraft/lidarr-on-ultra-steroids:latest
        restart: unless-stopped
        ports:
          - "8686:8686"
          - "6595:6595"
        volumes:
          - <path>:/config
          - <path>:/config_deemix
          - <path>:/downloads
          - <path>:/music

### Docker Run
    docker run \
      --name lidarr \
      -p 8686:8686 \
      -p 6595:6595 \
      -v <path>:/config \
      -v <path>:/config_deemix \
      -v <path>:/downloads \
      -v <path>:/music \
      --restart unless-stopped \
      ghcr.io/crapcraft/lidarr-on-ultra-steroids:latest

## Parameters
| Parameter | Function |
| :----: | --- |
| `-p 8686` | Lidarr WebUI |
| `-p 6595` | Deemix WebUI |
| `-e PUID=1000` | for UserID |
| `-e PGID=1000` | for GroupID |
| `-e AUTOCONFIG=true` | Enable automatic configuration |
| `-e FLAC2CUSTOM_ARGS=""` | Sets arguments used when calling flac2custom.sh |
| `-e CLEAN_DOWNLOADS=true` | Enable cleaning empty folders in /downloads |
| `-v /config` | Configuration files for Lidarr |
| `-v /config_deemix` | Configuration files for Deemix |
| `-v /downloads` | Path to your download folder for music |
| `-v /music` | Music files |

## Automatic configuration
In `AUTOCONFIG` mode (default), fill your Deezer credentials in the Deemix web UI (port 6595). Once `/config_deemix/login.json` is populated with your ARL, setup will automatically configure:
- /music root folder
- Delay profile allowing Deemix for automatic search
- Deemix as an indexer and download client
- Flac2Custom script connection if `FLAC2CUSTOM_ARGS` is set
- clean-downloads script connection

Set `AUTOCONFIG=false` to skip this.

## Audio conversion
Supports conversion from any format via ffmpeg. To enable, create a Custom Script connection in Lidarr. See [lidarr-flac2mp3](https://github.com/youegraillot/lidarr-flac2mp3) for details.

## Acknowledgments
- [youegraillot](https://github.com/youegraillot/lidarr-on-steroids) for the original lidarr-on-steroids
- [Lidarr](https://github.com/Lidarr/Lidarr) and [ta264](https://github.com/ta264) for plugin integration
- [lidarr-flac2mp3](https://github.com/TheCaptain989/lidarr-flac2mp3) for the conversion script
- [Deemix](https://deemix.org/) for the downloader backend
- [hotio](https://hotio.dev/) for the base docker image
