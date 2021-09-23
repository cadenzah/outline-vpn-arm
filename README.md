# outline-vpn-arm (forked)

Customized script for installing [Outline VPN](https://github.com/Jigsaw-Code/outline-server) on Raspberry Pi 4(ARM64) for personal use

## How to use

```bash
sudo ./INSTALL-ARM64 <DOMAIN_FOR_YOUR_SERVER>
```

- The argument is optional
- Put `sudo` at the beginning otherwise you will get permission error from `chmod`
- **[IMPORTANT]** DO NOT PUT `http://` or `https://` for `<DOMAIN_FOR_YOUR_SERVER> otherwise the init process will fail
