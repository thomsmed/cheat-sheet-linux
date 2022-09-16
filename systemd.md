# systemd - The System and Service manager

[systemd](https://systemd.io/)

- Basics about systemd Unit files: https://www.freedesktop.org/software/systemd/man/systemd.unit.html
- systemd Service Unit configuration: https://www.freedesktop.org/software/systemd/man/systemd.service.html
- Linux bootup and systemd boot targets: https://www.freedesktop.org/software/systemd/man/bootup.html

## systemd Unit files

Basic Unit file (for a Service Unit):
> The location of the file depends, but might be located at `/usr/local/lib/systemd/system`.

```txt
# myservice.service

[Unit]
Description=My Awesome Program

[Service]
Type=simple
ExecStart=/usr/bin/myProgram

[Install]
WantedBy=myotherservice.service
WantedBy=multi-user.target
```

```bash
# Manually start / stop service units
systemctl start myservice.service
systemctl stop myservice.service
```
