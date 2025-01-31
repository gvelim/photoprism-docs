# Running PhotoPrism on a Raspberry Pi

Our [stable version](../release-notes.md) and development preview now come as a single
[multi-arch image](https://hub.docker.com/r/photoprism/photoprism) for AMD64, ARM64, and ARMv7. If your device meets the system requirements, mostly the same [installation instructions](docker-compose.md)
as for regular Linux servers apply.

!!! info ""
    Use `photoprism/photoprism:latest` for the stable release or `photoprism/photoprism:preview` for testing 
    preview builds. Make sure to [pull the most recent image](updates.md) from Docker Hub. Existing users are advised to 
    update their `docker-compose.yml` config based on our examples for [ARM64](https://dl.photoprism.org/docker/arm64/docker-compose.yml)
    and [ARMv7](https://dl.photoprism.org/docker/armv7/docker-compose.yml)-based devices.

!!! missing ""
    Owners of [ARMv7-based devices](https://dl.photoprism.org/docker/armv7/docker-compose.yml) have to revert to 
    an [alternative image](https://hub.docker.com/r/linuxserver/mariadb) if they want to use MariaDB. 
    The [official image](https://hub.docker.com/_/mariadb) is available for AMD64 and ARM64 only. 
    Pay close attention to changed directory and environment variable names.

### System Requirements ###

- Your device should have at least 4 GB of memory. Running PhotoPrism on a server with [less than 4 GB of swap space](troubleshooting.md#adding-swap)
  or setting a memory/swap limit can cause unexpected restarts, especially when the indexer temporarily needs more
  memory to process large files.
- It's important to [boot](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) your 
  Raspberry Pi 3 / 4 with the parameter `arm_64bit=1` in `config.txt` to use our ARM64 image.
  Alternatively, you may run it on [UbuntuDockerPi](https://github.com/guysoft/UbuntuDockerPi).
  It's a 64bit Ubuntu Server with Docker pre-installed.
- If you see Docker errors related to "cgroups", it may help to add the following to `/boot/firmware/cmdline.txt`:
  ```
  cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1
  ```
- We [recommend](troubleshooting.md#linux-kernel-security) disabling Linux kernel security in your 
  [docker-compose.yml](https://dl.photoprism.org/docker/arm64/docker-compose.yml), especially if you do 
  not have experience with the configuration:
  ```yaml
  photoprism:
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
  ```
- If you install PhotoPrism on a public server outside your home network, please always run it behind a secure
  HTTPS reverse proxy such as [Traefik](proxies/traefik.md), [Caddy](proxies/caddy-2.md), or [NGINX](proxies/nginx.md).
  Your files and passwords will otherwise be transmitted in clear text and can be intercepted by anyone, 
  including your provider, hackers, and governments.

!!! note ""
    Indexing large photo and video collections significantly benefits from fast, local SSD storage,
    and plenty of memory for caching. Especially the conversion of RAW images and the transcoding of
    videos are very demanding.

!!! info ""
    If you're running out of memory - or other system resources - while indexing, try reducing the
    [number of workers](https://docs.photoprism.org/getting-started/config-options/) by setting
    `PHOTOPRISM_WORKERS` to a reasonably small value in `docker-compose.yml` (depending on the performance of the server).
    As a measure of last resort, you may disable using TensorFlow for image classification and facial recognition.

Big thank you to [Guy Sheffer](https://github.com/guysoft) for
[building](https://github.com/photoprism/photoprism/issues/109) this!
