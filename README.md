# Printer Monitor

One web page that shows all your Klipper/Moonraker printers on one screen:
camera, remote screen, print status and the spool in each lane.

It is a single `index.html` with no build step and no backend. Open it from
any static web server, or straight from disk.

## What it shows

- **A tile for each printer**, with its MJPEG camera stream and, where the
  printer has one, its remote screen page (Zmod and Snapmaker U1 serve it at
  `/screen/`). Click a tile to enlarge it.
- **Camera orientation** follows each printer's Moonraker webcam settings
  (rotation and flips, as set in Mainsail or Fluidd), unless you set a rotation
  or flip for that printer here.
- **Print status from Moonraker**: state, progress, ETA and temperatures. The
  printer's name and logo come from its Mainsail unless you set your own.
- **Spools row**: one entry per physical lane, built from Moonraker's
  `lane_data` and AFC lane state. Names, colors and weights come from
  Spoolman. Empty lanes show as Empty.
- **Spoolman location sync** (optional): sets each loaded spool's Spoolman
  location to `<printer> <lane>` and clears locations that are out of date.
  A spool claimed by two printers is flagged and skipped.

## Setup

1. Put `index.html` on a web server, for example nginx or
   `python -m http.server`, or just open the file.
2. Click **Printers…** and enter each printer's host, camera stream path and
   screen path. Set the Spoolman URL, or leave it empty to turn spools off.
   Settings are kept in your browser. **Export…** and **Import…** move them
   to another browser.

   The printers listed in `DEFAULT_PRINTERS` at the top of the script are
   the author's own. Replace them with yours, or set yours up in the page.
3. The page calls Moonraker on port 7125 from the browser, so each printer's
   `moonraker.conf` has to allow the page's origin:

   ```ini
   [authorization]
   cors_domains:
       http://your-monitor-host:8080
   ```

   Spoolman needs CORS allowed as well (`SPOOLMAN_CORS_ORIGIN`).

Tested with Zmod (Flashforge AD5X / Creator 5), Kiln OS, Snapmaker U1, the
FLSUN T1 and a stock Klipper/Mainsail Pi.

## License

GPL-3.0
