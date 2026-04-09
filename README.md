# Lenovo KnowledgeBase

Internal one-pager for the Lenovo IN CEC.

## Project Structure

```
lenovo-kb/
├── app.py               ← Flask application entry point
├── requirements.txt     ← Python dependencies
├── templates/
│   └── index.html       ← Main HTML template
└── static/
    ├── css/style.css    ← Stylesheet
    └── js/main.js       ← Client-side JS
```

## Quick Start (Local)

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Run the app
python app.py
# → Open http://localhost:5000
```

## Production Deployment

### Option A — Gunicorn + Nginx (Linux VPS)

```bash
pip install gunicorn
gunicorn -w 4 -b 0.0.0.0:8000 app:app
```

Point Nginx to `localhost:8000`. Sample Nginx block:

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location /static/ {
        alias /path/to/lenovo-kb/static/;
        expires 7d;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Option B — Docker

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt gunicorn
EXPOSE 8000
CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:8000", "app:app"]
```

```bash
docker build -t lenovo-kb .
docker run -p 8000:8000 lenovo-kb
```

### Option C — Static (No Python Required)

If you prefer pure static hosting (GitHub Pages, Netlify, Vercel),
rename `templates/index.html` to `index.html`, move it to the project root,
update all `{{ url_for('static', ...) }}` references to relative paths
like `./static/css/style.css` and `./static/js/main.js`, then deploy.
