# systemd - The System and Service manager

[systemd](https://systemd.io/)

- Basics about systemd Unit files: https://www.freedesktop.org/software/systemd/man/systemd.unit.html
- systemd Service Unit configuration: https://www.freedesktop.org/software/systemd/man/systemd.service.html
- Linux bootup and systemd boot targets: https://www.freedesktop.org/software/systemd/man/bootup.html

## systemd Unit files

Common systemd Unit file locations (Unit files found in directories listed earlier override files in directories lower in the list):
- `/etc/systemd/system`. Meant for custom system wide Units (created by system admins).
- `/usr/local/lib/systemd/system`. Meant for custom system wide Units (installed by system admins).
- `/usr/lib/systemd/system` or `/lib/systemd/system`. Do not put custom Unit files here (meant for the system itself).

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

```bash
# Configure the service unit to automatically start on system boot
systemctl enable myservice.service
```

```bash
# Check service status
systemctl status myservice.service
```

```bash
# Read output from service (stdout/stderr)
sudo journalctl -u myservice.service
```
