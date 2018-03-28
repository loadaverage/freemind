![Docker build status](https://img.shields.io/docker/build/loadaverage/freemind.svg)
![Docker automated](https://img.shields.io/docker/automated/loadaverage/freemind.svg)
### Dockerized Freemind - free mind mapping software, based on Alpine Linux, uses X11 socket

Requirements:
---
- [Docker](https://github.com/docker/docker)
- xhost (may be needed on some Linux distros)

How to:
---
- Build image  
`docker build -t freemind .`  
**or**  
- Pull image from Docker Registry  
`docker pull loadaverage/freemind`
- Allow access to X server (for some Linux distros)  
`xhost local:freemind`
- Run own FreeMind image

 ```bash
docker run --rm \
      -v ~/Downloads/freemind:/home/freemind/Downloads \
      -v ~/.freemind:/home/freemind/.freemind/ \
      -v ~/.themes:/home/freemind/.themes:ro \
      -v ~/.fonts:/home/freemind/.fonts:ro \
      -v ~/.icons:/home/freemind/.icons:ro \
      -v /usr/share/themes:/usr/share/themes:ro \
      -v /usr/share/fonts:/usr/share/fonts:ro \
      -v /tmp/.X11-unix:/tmp/.X11-unix \
      -e DISPLAY=$DISPLAY freemind
```

- Run FreeMind image from Docker Registry  
```bash
docker run --rm \
      -v ~/Downloads/freemind:/home/freemind/Downloads \
      -v ~/.freemind:/home/freemind/.freemind/ \
      -v ~/.themes:/home/freemind/.themes:ro \
      -v ~/.fonts:/home/freemind/.fonts:ro \
      -v ~/.icons:/home/freemind/.icons:ro \
      -v /usr/share/themes:/usr/share/themes:ro \
      -v /usr/share/fonts:/usr/share/fonts:ro \
      -v /tmp/.X11-unix:/tmp/.X11-unix \
      -e DISPLAY=$DISPLAY loadaverage/freemind
```
- Docker for Mac

Note: UNIX sockets in Docker for Mac are not [yet](https://github.com/docker/for-mac/issues/483) supported, thus X11 network forwarding is the only way to go

- Allow access to the X11
```bash
export X11_IP=$(ifconfig en0 | grep [i]net | awk '$1 == "inet" {print $2}')
xhost +$X11_IP
```
**or**
- Use socat for UNIX socket -> TCP port forwarding
```bash
socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"
```
```bash
docker run --rm \
      -v ~/Downloads/freemind:/home/freemind/Downloads \
      -v ~/.freemind:/home/freemind/.freemind/ \
      -v ~/.fonts:/home/freemind/.fonts:ro \
      -e DISPLAY=$X11_IP:0 loadaverage/freemind
```

**NOTES:**
- Volumes: mounted config directories should have correct permissions, otherwise Freemind will not work properly
- Xhost: on Linux distros you may need explicitly allow access to X server through xhost command: xhost local:freemind
- Fonts: font settings for JRE: https://wiki.archlinux.org/index.php/Java_Runtime_Environment_fonts#Anti-aliasing
