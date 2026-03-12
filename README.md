# Local File Server (FileBrowser)

A lightweight, browser-based file server for your LAN using FileBrowser. Supports authenticated access, large file uploads (TUS chunking with resume), and runs as a user-level systemd service.

## Demo
![Web UI](docs/ui.png)

Replace `docs/ui.png` with a screenshot or add `docs/demo.gif` for an animated demo. See `docs/capture-demo.md` for quick capture commands.

## Layout
- Root directory: `/media/lab2208/ssd/local_server`
- Share path (web root): `shared/`
- Binary: `bin/filebrowser` (ignored from git)
- Database/config: `fb_data/filebrowser.db` (ignored from git)
- Systemd unit (copy): `systemd/filebrowser.service`

## Quick Start
1) Open firewall for the web UI:
   - `sudo ufw allow 8090/tcp && sudo ufw reload`

2) Start/enable the service (user scope):
   - `systemctl --user start filebrowser`
   - `systemctl --user enable filebrowser`

3) Access from LAN:
   - URL: `http://<server-ip>:8090`
   - Default admin username: `admin`

   The current admin password is saved locally (not in git) at:
   - `/media/lab2208/ssd/local_server/fb_admin_password.txt`

## Service Unit
A reference unit is included at `systemd/filebrowser.service`. It ensures the SSD is mounted before starting and points to the correct paths. To install/update it for the current user:

```
mkdir -p ~/.config/systemd/user
cp systemd/filebrowser.service ~/.config/systemd/user/filebrowser.service
systemctl --user daemon-reload
systemctl --user enable --now filebrowser
```

## SSD Auto-Mount (fstab)
To auto-mount the SSD at boot (ext4 example):

```
sudo cp /etc/fstab /etc/fstab.backup.$(date +%F-%H%M)
echo 'UUID=ce6e7ec8-921b-4d5f-91f0-da3c04b57a56 /media/lab2208/ssd ext4 noatime,nofail 0 2' | sudo tee -a /etc/fstab
sudo mkdir -p /media/lab2208/ssd && sudo chown $USER:$USER /media/lab2208/ssd
sudo mount -a
```

## Managing Users
Use the FileBrowser CLI from the project root:

```
./bin/filebrowser -d fb_data/filebrowser.db users add <username> <password> --scope /
./bin/filebrowser -d fb_data/filebrowser.db users update <id|username> -p <newpass>
./bin/filebrowser -d fb_data/filebrowser.db users ls
```

Stop the service first if the DB is locked:

```
systemctl --user stop filebrowser
# ...run CLI commands...
systemctl --user start filebrowser
```

## Logs and Status
- Status: `systemctl --user status filebrowser`
- Live logs: `journalctl --user -u filebrowser -f`

## Notes
- Large uploads use TUS chunking (default 100 MB chunks; configurable).
- For LAN discoverability, you can optionally add an Avahi service file to advertise `http://<hostname>.local:8090`.
- Security: Passwords and the database are ignored by git to avoid leaking secrets.
