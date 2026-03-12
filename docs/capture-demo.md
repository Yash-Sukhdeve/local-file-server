## Capture a Screenshot or GIF of the Web UI

The FileBrowser UI runs at `http://<server-ip>:8090`.

### Screenshot
- GNOME Screenshot: `gnome-screenshot -a -f docs/ui.png`
- Flameshot (recommended): `flameshot gui --path docs/ui.png`

### Animated GIF (with ffmpeg)
1. Record a short MP4 of a window region (replace X,Y and size):
   ```bash
   ffmpeg -video_size 1280x720 -framerate 30 -f x11grab -i :0.0+X,Y -t 20 docs/demo.mp4
   ```
2. Convert MP4 to optimized GIF:
   ```bash
   ffmpeg -y -i docs/demo.mp4 -vf "fps=12,scale=1280:-1:flags=lanczos,palettegen" docs/palette.png
   ffmpeg -y -i docs/demo.mp4 -i docs/palette.png -filter_complex "fps=12,scale=1280:-1:flags=lanczos[x];[x][1:v]paletteuse" docs/demo.gif
   ```

Then update the README to point to `docs/demo.gif` if desired.

