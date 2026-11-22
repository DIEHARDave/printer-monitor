> **Moved:** this project is now part of [PrintWarden](https://github.com/DIEHARDave/PrintWarden) (private for now), together with TimelapseOffload and Print Watch. This repository is archived and no longer updated.

# Printer Monitor

One web page that shows all your Klipper/Moonraker printers on one screen:
camera, remote screen, print status and the spool in each lane.

It is a single `index.html` with no build step and no backend. Open it from
any static web server, or straight from disk.

![Printer Monitor showing five printers: camera, remote screen, print preview, temperatures and spools for each](docs/screenshot.png)

## What it shows

- **A tile for each printer**, with its MJPEG camera stream and, where the
  printer has one, its remote screen page (Zmod and Snapmaker U1 serve it at
  `/screen/`). Click a tile to enlarge it.
- **Print status from Moonraker**: state, progress, ETA and temperatures. The
  printer's name and logo come from its Mainsail unless you set your own.
- **Spools row**: one entry per physical lane, built from Moonraker's
  `lane_data` and AFC lane state. Names, colors and weights come from
  Spoolman. Empty lanes show as Empty.
- **Spoolman location sync** (optional): sets each loaded spool's Spoolman
  location to `<printer> <lane>` and clears locations that are out of date.
  A spool claimed by two printers is flagged and skipped.
- **Rows you arrange**: each printer's column has Camera, Screen, Card,
  Spools and AI Monitor rows. The slim bar on the left of each row has a
  grip (drag it onto another row to move it) and a fold arrow (fold the row
  to a thin strip; it keeps running). The **Rows** menu in the toolbar
  switches rows on or off; rows switched off stop their streams and polling.
  These apply to every printer alike, and every row is as tall as its
  tallest copy (smaller content sits at the top), so rows and columns stay level.
  Card temperature labels shorten to E0/E1/HB when a tile is too narrow.
- **Fit to window**: **Width** sizes the tiles to fill the window's width in
  one line and lets the page scroll down; **Height** fits everything in the
  window without scrolling.
- **AI Monitor** (optional): when the page is served by TimelapseOffload
  (the author's timelapse and failure-detection container, not public, which
  has its own copy of this page), it reads that server's detection status
  from `api/status`. The detectors' boxes are drawn over each live camera
  in the label page's problem-type colors (red spaghetti, orange dashed
  moved part, each named problem type in its own color). Each
  printer's AI Monitor card has a timelapse chip and two detection chips,
  **spaghetti** and **moved**, each clicked through off, alert and
  alert + pause; the failure score and its history, the part-position check,
  the problem types seen and **Not a failure**; and the alert history with
  this print's newest six flagged frames still to review, with what was
  detected drawn on them (a link opens all of them; clicking one opens it on
  the server's label page, and once marked it leaves the list). The tile border turns
  orange or red on an alert, a camera button on the live camera captures a
  frame for labelling, and **Printers…** gets the server's detection, alert
  and timelapse settings under each printer's row (the Discord webhook is
  write-only: it is never shown once saved). Served anywhere else, that
  request fails quietly and the AI Monitor row is not offered.

## Screenshots

Five printers with the AI Monitor row on, served by TimelapseOffload. The
dashed boxes on the cameras are the part-position check's changed areas
(drawn red before boxes took the problem-type colors; orange now):

![Five printer columns with camera, AI Monitor card, remote screen, print preview, temperatures and spools; red dashed boxes on two cameras](docs/ai-monitor-overview.png)

A moved-part alert: the tile border turns red, the AI Monitor card shows
"Part moved or came loose" with its flagged frames, and the alert pops up
on the printer's own screen:

![One printer column during a moved-part alert, with flagged frames in the activity list and a Printer Error dialog on the remote screen](docs/moved-part-alert.png)

A flagged frame opened on TimelapseOffload's label page: the detectors' boxes
are shown dashed in their problem type's color (red was the part-position
color when this was taken; it is orange now), and you box the problem, pick
its type and mark the frame as a problem, fine or not sure:

![Label frame page with dashed detector boxes on the camera picture and the problem type list, boxes and verdict on the right](docs/label-frame.png)

## Setup

1. Put `index.html` on a web server, for example nginx or
   `python -m http.server`, or just open the file.
2. Click **Printers…** and enter each printer's host, then pick its camera
   and screen from the lists (they come from the printer's Moonraker webcam
   list, as set up in Mainsail; **Custom…** takes a typed path or URL). Set the Spoolman URL, or leave it empty to turn spools off.
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
