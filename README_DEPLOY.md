# AC-174 SIR Dashboard — hosting on AWS (static, no database, no API)

This is a **fully static** site: an HTML file plus a JSON data file. There is no
backend application, no database, and no server-side code — the "backend" is just
any web server handing out static files over HTTP GET. That's all the browser needs.

```
ac174_hosted/
  index.html          <- the dashboard (fetches ./data/ac174.json)
  data/ac174.json     <- the data (edit + re-serve to update; no HTML change needed)
  README_DEPLOY.md    <- this file
```

The dashboard fetches `./data/ac174.json` at load. To refresh the numbers later,
regenerate just the JSON (`python build_ac174.py`) and drop it in place — the page
picks it up on next load.

## Option A — EC2 + Elastic IP with nginx (recommended)

1. **Launch an EC2 instance** (Amazon Linux 2023 or Ubuntu; `t3.micro` is plenty).
2. **Allocate an Elastic IP** and associate it with the instance
   (EC2 → Elastic IPs → Allocate → Associate). This gives a fixed public IP.
3. **Security group:** allow inbound **TCP 80** (HTTP) from `0.0.0.0/0`
   (and 443 later if you add TLS). Keep 22 (SSH) restricted to your IP.
4. **Install nginx and copy the files:**
   ```bash
   # Amazon Linux 2023
   sudo dnf install -y nginx        # (Ubuntu: sudo apt update && sudo apt install -y nginx)
   sudo systemctl enable --now nginx
   # copy the bundle to the web root
   sudo cp -r index.html data /usr/share/nginx/html/   # (Ubuntu web root: /var/www/html/)
   ```
5. **Visit** `http://<your-elastic-ip>/` — done.

To update data: `sudo cp data/ac174.json /usr/share/nginx/html/data/ac174.json`.

nginx already sends correct `Content-Type: application/json` for `.json` and
`text/html` for the page, so no extra config is required.

## Option B — dead-simple, no nginx (quick demo)

On the instance, from inside the bundle directory:
```bash
python3 -m http.server 80 --bind 0.0.0.0
```
Serves the current folder on port 80. Fine for a quick check; for anything
persistent use nginx (Option A) or run it under `systemd`/`tmux` so it survives
logout. Ensure the security group allows port 80.

## Option C — S3 + CloudFront (no server at all)

If you don't need the Elastic IP specifically: upload `index.html` and
`data/ac174.json` (preserving the `data/` prefix) to an S3 bucket, enable static
website hosting (or front it with CloudFront), and browse the bucket/CDN URL. Set
`data/ac174.json` content-type to `application/json`. Cheapest and zero-maintenance.

## Notes
- **Offline copy:** `AC174_SIR_Dashboard.html` (one directory up) has the data
  embedded and opens by double-click with no server — hand that to anyone who just
  needs to view it locally. This hosted `index.html` is for the shared AWS URL.
- **HTTPS:** browsers are happy serving this over plain HTTP on an IP. If you want
  `https://`, put a domain in front and add a free certificate via
  `certbot --nginx`, or use CloudFront (Option C) which provides TLS by default.
- **Same code, two modes:** `index.html` prefers the fetched JSON (so hosted data
  can be updated independently) and falls back to any embedded blob — that's why
  the identical front-end powers both the hosted site and the offline file.
