# Local Server Commands for Sliding Window Tutorial

## Quick Start

### Method 1: Python HTTP Server (Recommended)

**Start the server:**
```bash
cd /Users/mohammadreza/Misc
python3 -m http.server 8005
```

**Access the tutorial:**
Open in your browser: `http://localhost:8005/sliding-window-tutorial.html`

**Stop the server:**
Press `Ctrl + C` in the terminal, or run:
```bash
lsof -ti:8005 | xargs kill
```

---

### Method 2: Python HTTP Server (Different Port)

If port 8000 is busy, use a different port:
```bash
cd /Users/mohammadreza/Misc
python3 -m http.server 8080
```
Then access: `http://localhost:8080/sliding-window-tutorial.html`

---

### Method 3: Node.js HTTP Server (If you have Node.js)

**Install http-server globally (one-time):**
```bash
npm install -g http-server
```

**Start the server:**
```bash
cd /Users/mohammadreza/Misc
http-server -p 8000
```

**Access the tutorial:**
Open in your browser: `http://localhost:8000/sliding-window-tutorial.html`

**Stop the server:**
Press `Ctrl + C` in the terminal

---

### Method 4: PHP Built-in Server (If you have PHP)

```bash
cd /Users/mohammadreza/Misc
php -S localhost:8000
```

Access: `http://localhost:8000/sliding-window-tutorial.html`

---

## Troubleshooting

**Port already in use?**
- Use a different port (8080, 3000, etc.)
- Or kill the process using the port:
  ```bash
  lsof -ti:8000 | xargs kill
  ```

**Can't find Python?**
- Try `python` instead of `python3`
- Or check if Python is installed: `python3 --version`

**Want to run in background?**
Add `&` at the end:
```bash
python3 -m http.server 8000 &
```

---

## Quick Reference

| Method | Command | URL |
|--------|---------|-----|
| Python | `python3 -m http.server 8000` | http://localhost:8000/sliding-window-tutorial.html |
| Node.js | `http-server -p 8000` | http://localhost:8000/sliding-window-tutorial.html |
| PHP | `php -S localhost:8000` | http://localhost:8000/sliding-window-tutorial.html |

---

## Notes

- The server must be running in the same directory as `sliding-window-tutorial.html`
- Keep the terminal window open while using the tutorial
- To stop any server, press `Ctrl + C` in the terminal where it's running

