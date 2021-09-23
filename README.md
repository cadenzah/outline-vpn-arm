# outline-vpn-arm (forked)

Customized script for installing [Outline VPN](https://github.com/Jigsaw-Code/outline-server) on Raspberry Pi 4(ARM64) for personal use

## How to use

```bash
sudo ./INSTALL-ARM64 <DOMAIN_FOR_YOUR_SERVER>
```

- The <DOMAIN> argument is optional
- Put `sudo` at the beginning otherwise you will get permission error from `chmod`
- **[IMPORTANT]** DO NOT PUT `http://` or `https://` for `<DOMAIN_FOR_YOUR_SERVER> otherwise the init process will fail

## Tested environment
- Raspi 4 (ARM64)
- [Ubuntu 18.04](http://old-releases.ubuntu.com/releases/18.04.4/)
- Docker
    - Docker version 20.10.7
    - build 20.10.7-0ubuntu1~18.04.1
    - `sudo apt-get install docker.io` ([Issue with installing Docker recent build](https://github.com/Jigsaw-Code/outline-server/issues/951#issuecomment-910724432))
