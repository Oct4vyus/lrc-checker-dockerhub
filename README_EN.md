- [Spanish](README.md)

# 🎵 LRC Checker

If you love listening to music from your own server and you actually care about the lyrics, you know the story.
You install a few apps, add some plugins, and suddenly you’re dealing with missing lyrics, out-of-sync lines, weird versions, and tracks that don’t match the real song.

And when your library gets big, it becomes a mess pretty fast.
How do you know which albums are clean, which ones need work, and how many tracks are actually off?

This tool was made to make that easier.
It works from any browser, and lets you check, sort, fix, and clean up synced lyrics in your music collection without jumping between apps or doing all the work manually.

It scans your library and generates a visual report called `lrc_report.html` with categories and quick actions to edit `.lrc` files.

---
## ✨ How to scan your music folders
There are a few easy ways:
- The first time the container starts correctly, it creates a log and does the first scan automatically.
- You can trigger a scan from the browser at *http://SERVER-IP:PORT/escanear*
- Or you can do it straight from the report itself with the **“Scan now”** button at the bottom

## ✨ How it works

The app compares the last timestamp in the `.lrc` file with the real song duration, using a tolerance you can set in the Docker config.
```bash
# Difference in seconds before it counts as a real sync problem
      - REVIEW_THRESHOLD_SEC=60.0
```

Then it sorts everything into categories inside `lrc_report.html`.
Most of the results are grouped automatically, and a couple of categories are added when you mark files yourself.

The nice part is that the categories are clickable. Instead of looking at a giant list, you can filter the report by type and focus on the songs that actually need attention.

---

## 📊 The report (`lrc_report.html`)

The report groups files into categories like these:

- **All** — shows every audio file that was scanned
- ✅ OK — the `.lrc` file exists, is valid, and is synced or already checked
- ❌ Missing — there is no `.lrc` file for that track
- ⚠️ Empty — the file exists but has no useful content
- ⚠️ Unsynced — the lyrics have no timestamps
- ❌ Corrupt — the file is unreadable or badly formatted
- ℹ️ Orphans — `.lrc` files that don’t have a matching audio file

**Categories based on timing differences**
- 🔴 Desync — the last timestamp is a bit off from the actual song duration
- 🟡 Review — the difference is big enough to deserve attention

**Categories based on what you do**
- ✍️ Signed — a file was reviewed and fixed, then marked so it can be recognized later and moved to OK
- 🎼 INSTRUMENTAL — the track is marked as instrumental, so it is clearly identified as having no lyrics

Example of the signature setup in `docker-compose.yml`:
```bash
environment:
  - SIGNED_MARKER=Oct4vyus Kandle
```
If you don’t set one, `Oct4vyus Kandle` is used by default.

## ✨ Working with the results table
You can do a few useful things directly from the table:
- Blue rows group whole albums together. Under each album, you can view and edit every track with the **“EDIT”** button.
- From those album rows, you can mark all tracks in the album as ✍️ Signed or 🎼 INSTRUMENTAL.
- In the AUDIO and .LRC columns, you can click the files to download them to your device.
- The **“EDIT”** button opens a full editor so you can create or repair any `.lrc` file.

If you want to know more about the editor, you can download the user guide.

## 📖 User guide
You can download the complete manual here:
[Download user guide](docs/manual-uso-editor-lrc-en.pdf)

---
## 🔧 Getting started
Edit the `docker-compose.yml` file with your own values:
```bash
services:
  lrc-checker:
    image: oct4vyus/lrc-checker:latest
    container_name: lrc-checker
    restart: unless-stopped

    # Configurable environment variables
    environment:
      # Set your timezone
      - TZ=America/Argentina/Cordoba
      # Set the PUID/PGID values. Use the user/group that can edit the LRC files
      - PUID=0
      - PGID=0
      # Folder where your music is located inside the container
      - MUSIC_DIR=/music
      # Folder where reports will be saved
      - OUTPUT_DIR=/reports
      # Difference in seconds before it counts as a real sync problem
      - REVIEW_THRESHOLD_SEC=60.0
      # Check for .lrc files that have no matching audio file
      - CHECK_ORPHANS=true
      # Show detailed progress in the logs
      - VERBOSE=true
      - SERVER_URL=http://CHANGE-THIS-TO-YOUR-SERVER-IP:8080
      # Whatever comes after the = will be the signature
      - SIGNED_MARKER=Oct4vyus Kandle
      # Optional. If you do not set it, "Oct4vyus Kandle" is used

    # Volumes: mount your music library and the reports folder
    volumes:
      # CHANGE THIS PATH to your real music library path
      - /path/to/your/music:/music:rw
      # Folder where reports will be saved (JSON and HTML)
      - ./reports:/reports

    ports:
      - "8080:8080" # Change the port if needed

    # Optional: limit resources
    # deploy:
    #   resources:
    #     limits:
    #       cpus: '0.5'
    #       memory: 256M
```

## ⚡ Quick start

1. Set the basics in `docker-compose.yml`:
   - your music folder
   - where reports will be saved
   - the server IP/port
2. Start the container.

```bash
docker-compose up -d
```

Then open this in your browser:

3. Check the service:

http://SERVER-IP:PORT/status
It should say **OK**.

4. Run the first scan:

http://SERVER-IP:PORT/escanear

On big libraries, this can take a while. When it finishes, you’ll see something like:
*Full scan: XX file(s) in REVIEW, XX OK out of XX total.*

5. Open `lrc_report.html` in the reports folder and start fixing the ones that need it.

---

🤝 Contributions
Contributions are welcome. If you have an idea, improvement, or bug report, feel free to open an issue or send a pull request.

📜 License
MIT License

## 🌐 Community

If you want to swap LRC files, share experiences, or ask for help, you can join the Telegram group:
- https://t.me/+Vzi9agLuVyo2MDcx

## Screenshot of the report

This is an example of a report generated by **LRC Checker v6.2.0**:

![Screenshot of the report](https://github.com/Oct4vyus/lrc-checker-dockerhub/raw/main/docs/imagenes/lrc-checker-report.png)

## 💖 Support the project
If you want to help keep it going, you can support it here:

- TRON: TN6foPkepP2qBqKQHsu1T2rDeYgm5uTThN
- BNB Chain: 0x8916c1b53Cc039672066bc4A297aa048CC7f1ae3
