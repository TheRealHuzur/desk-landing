# Aufgabe: DESK-Suite Landing Page bauen und deployen

Baue die Landing Page für die DESK-Suite exakt nach den folgenden Vorgaben. Der gesamte Code kommt in **eine einzige Datei: `index.html`**. Kein Build-Prozess, kein Framework, kein Node.js.

---

## Technischer Kontext

- Die Seite wird unter `desk-suite.wissen-und-werkzeug.de` erreichbar sein
- Deployment: Coolify (Static HTML) aus einem GitHub-Repo
- Coolify deployed automatisch per Git-Push – kein Dockerfile nötig
- SSL wird automatisch von Traefik (Let's Encrypt) übernommen
- Die Seite ist die **Supabase Site URL** – sie muss einen OAuth-Callback verarbeiten können
- Dafür brauchen wir zusätzlich eine zweite Datei: `auth/callback.html` (siehe unten)

---

## Repo-Struktur

```
desk-landing/
├── index.html
└── auth/
    └── callback.html
```

---

## index.html – Exakte Vorlage

Baue die Seite **pixel-genau** nach diesem HTML. Ändere nichts an Struktur, CSS oder Texten:

```html
<!DOCTYPE html>
<html lang="de" data-theme="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DESK-Suite – Wissen & Werkzeug</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
  <style>
    :root {
      --teal: #0e97ad;
      --sky: #0ea5e9;
      --light: #b0cfe5;
      --navy: #2a314d;
      --deep: #0B162A;
      --bg: #020617;
      --bg-2: #1a2235;
      --bg-3: #1e2c42;
      --text: #e8eef5;
      --text-muted: #7a91ab;
      --border: rgba(176, 207, 229, 0.12);
      --glow: rgba(14, 151, 173, 0.18);
      --accent: var(--teal);
      --accent-2: var(--sky);
      --input-bg: #1e2c42;
      --shadow: 0 4px 24px rgba(0, 0, 0, 0.35);
      --shadow-sm: 0 2px 8px rgba(0, 0, 0, 0.25);
      --grid-color: rgba(14, 151, 173, 0.035);
    }

    [data-theme="light"] {
      --bg: #eef4fa;
      --bg-2: #ffffff;
      --bg-3: #e2ecf5;
      --text: #0f1c2e;
      --text-muted: #4e6680;
      --border: rgba(14, 97, 150, 0.13);
      --glow: rgba(14, 151, 173, 0.13);
      --accent: #0a7d94;
      --accent-2: #0284c7;
      --input-bg: #f5f9fd;
      --shadow: 0 4px 24px rgba(14, 60, 120, 0.10);
      --shadow-sm: 0 2px 10px rgba(14, 60, 120, 0.08);
      --grid-color: rgba(14, 97, 173, 0.045);
    }

    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { scroll-behavior: smooth; }

    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'Inter', sans-serif;
      font-size: 15px;
      line-height: 1.6;
      min-height: 100vh;
      transition: background 0.35s ease, color 0.35s ease;
    }

    body::before {
      content: '';
      position: fixed;
      inset: 0;
      background-image:
        linear-gradient(var(--grid-color) 1px, transparent 1px),
        linear-gradient(90deg, var(--grid-color) 1px, transparent 1px);
      background-size: 40px 40px;
      pointer-events: none;
      z-index: 0;
      transition: opacity 0.35s;
    }

    .page { position: relative; z-index: 1; }

    [data-theme="light"] header {
      background: linear-gradient(to bottom, rgba(238, 244, 250, 0.97), rgba(238, 244, 250, 0.82));
      border-bottom: 1px solid rgba(14, 151, 173, 0.18);
    }
    [data-theme="light"] .card {
      box-shadow: 0 2px 12px rgba(14, 60, 120, 0.08), inset 0 1px 0 rgba(255,255,255,0.8);
      border-color: rgba(14, 97, 150, 0.13);
    }
    [data-theme="light"] .card-clickable:hover {
      border-color: rgba(14, 151, 173, 0.45);
      box-shadow: 0 4px 20px rgba(14, 97, 150, 0.14), 0 0 0 1px rgba(14, 151, 173, 0.12), inset 0 1px 0 rgba(255,255,255,0.9);
      transform: translateY(-2px);
    }
    [data-theme="light"] .hero-title span {
      background: linear-gradient(135deg, #0a7d94, #0284c7);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }
    [data-theme="light"] .logo-dot { box-shadow: 0 0 6px rgba(10, 125, 148, 0.6); }

    .card, header, footer {
      transition: background 0.3s ease, border-color 0.3s ease, color 0.3s ease, box-shadow 0.3s ease;
    }

    header {
      background: color-mix(in srgb, var(--bg) 80%, transparent);
      backdrop-filter: blur(14px);
      -webkit-backdrop-filter: blur(14px);
      border-bottom: 1px solid var(--border);
      position: sticky;
      top: 0;
      z-index: 100;
    }

    .header-inner {
      max-width: 960px;
      margin: 0 auto;
      padding: 0 24px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      height: 56px;
    }

    .logo {
      font-family: 'Inter', sans-serif;
      font-size: 20px;
      font-weight: 800;
      color: var(--text);
      display: flex;
      align-items: center;
      gap: 0;
      text-decoration: none;
      letter-spacing: -0.01em;
    }

    .logo-dot {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      background: var(--teal);
      box-shadow: 0 0 8px var(--teal);
      animation: pulse 2.5s ease-in-out infinite;
      flex-shrink: 0;
      margin-right: 10px;
    }

    @keyframes pulse {
      0%, 100% { opacity: 1; box-shadow: 0 0 8px var(--teal); }
      50% { opacity: .5; box-shadow: 0 0 20px var(--teal); }
    }

    .header-right { display: flex; align-items: center; gap: 16px; }

    .theme-toggle { display: flex; align-items: center; gap: 8px; cursor: pointer; user-select: none; }
    .theme-label { font-size: 12px; color: var(--text-muted); font-weight: 500; min-width: 32px; transition: color 0.3s; }
    .toggle-track {
      width: 44px; height: 24px;
      background: var(--bg-3);
      border: 1px solid var(--border);
      border-radius: 12px;
      position: relative;
      cursor: pointer;
      transition: background 0.3s, border-color 0.3s;
    }
    .toggle-track.active {
      background: linear-gradient(135deg, var(--teal), var(--sky));
      border-color: var(--teal);
    }
    .toggle-thumb {
      width: 18px; height: 18px;
      background: #fff;
      border-radius: 50%;
      position: absolute;
      top: 2px; left: 2px;
      transition: transform 0.3s cubic-bezier(.34,1.56,.64,1);
      box-shadow: 0 1px 4px rgba(0,0,0,0.25);
      display: flex; align-items: center; justify-content: center;
      font-size: 10px;
    }
    .toggle-track.active .toggle-thumb { transform: translateX(20px); }

    main {
      max-width: 960px;
      margin: 0 auto;
      padding: 0 24px 80px;
    }

    .hero {
      padding: 80px 0 64px;
      text-align: center;
      animation: fadeUp 0.6s ease both;
    }

    .hero-eyebrow {
      font-size: 11px;
      font-weight: 600;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      color: var(--teal);
      margin-bottom: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 10px;
    }

    .hero-eyebrow::before,
    .hero-eyebrow::after {
      content: '';
      display: block;
      width: 32px;
      height: 1px;
      background: var(--teal);
      opacity: 0.5;
    }

    .hero-title {
      font-size: clamp(36px, 6vw, 58px);
      font-weight: 800;
      letter-spacing: -0.03em;
      line-height: 1.1;
      margin-bottom: 20px;
      color: var(--text);
    }

    .hero-title span {
      background: linear-gradient(135deg, var(--teal), var(--sky));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .hero-sub {
      font-size: 17px;
      color: var(--text-muted);
      max-width: 480px;
      margin: 0 auto 36px;
      font-weight: 400;
      line-height: 1.65;
    }

    .section-label {
      font-size: 11px;
      font-weight: 600;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--text-muted);
      margin-bottom: 20px;
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .section-label::after {
      content: '';
      flex: 1;
      height: 1px;
      background: var(--border);
    }

    .app-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap: 16px;
      margin-bottom: 56px;
    }

    .card {
      background: var(--bg-2);
      border: 1px solid var(--border);
      border-radius: 14px;
      padding: 28px;
      position: relative;
      overflow: hidden;
    }

    .card-clickable {
      cursor: pointer;
      text-decoration: none;
      display: block;
      color: inherit;
      transition: border-color 0.2s, box-shadow 0.2s, transform 0.2s;
    }

    .card-clickable:hover {
      border-color: rgba(14, 151, 173, 0.28);
      box-shadow: 0 0 30px var(--glow), var(--shadow-sm);
      transform: translateY(-2px);
    }

    .card-clickable::before {
      content: '';
      position: absolute;
      top: -1px; left: -1px; right: -1px;
      height: 2px;
      background: linear-gradient(90deg, transparent, var(--teal), transparent);
      opacity: 0;
      transition: opacity 0.3s;
      border-radius: 14px 14px 0 0;
    }

    .card-clickable:hover::before { opacity: 1; }

    .card-title {
      font-size: 22px;
      font-weight: 800;
      letter-spacing: -0.02em;
      margin-bottom: 8px;
      color: var(--text);
      line-height: 1.1;
    }

    .card-title span { color: var(--sky); }

    .card-desc {
      font-size: 13.5px;
      color: var(--text-muted);
      line-height: 1.55;
      margin-bottom: 4px;
    }

    .card-note {
      font-size: 12px;
      color: var(--text-muted);
      opacity: 0.7;
      margin-top: 8px;
      line-height: 1.5;
    }

    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(16px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    .app-grid .card:nth-child(1) { animation: fadeUp 0.5s 0.1s ease both; }

    footer {
      border-top: 1px solid var(--border);
      padding: 20px 24px;
      text-align: center;
      font-size: 12px;
      color: var(--text-muted);
    }

    footer a { color: var(--teal); text-decoration: none; }
    footer a:hover { text-decoration: underline; }
  </style>
</head>
<body>
<div class="page">

  <header>
    <div class="header-inner">
      <a href="/" class="logo">
        <div class="logo-dot"></div>DESK<span style="color:var(--sky)">suite</span>
      </a>
      <div class="header-right">
        <div class="theme-toggle" onclick="toggleTheme()">
          <span class="theme-label" id="themeLabel">Dark</span>
          <div class="toggle-track" id="toggleTrack">
            <div class="toggle-thumb" id="toggleThumb">🌙</div>
          </div>
        </div>
      </div>
    </div>
  </header>

  <main>
    <section class="hero">
      <div style="justify-content:center; margin-bottom:8px; font-size:22px; font-weight:800; display:flex; align-items:center; letter-spacing:-0.01em;">DESK<span style="color:var(--sky)">suite</span></div>
      <div class="hero-eyebrow">Wissen & Werkzeug</div>
      <h1 class="hero-title">Open-Source Tools</h1>
      <p class="hero-sub">Kleine Tools für den täglichen Wahnsinn.</p>
    </section>

    <div class="section-label">Tools</div>
    <div class="app-grid">
      <a href="https://goaldesk.wissen-und-werkzeug.de" class="card card-clickable">
        <div class="card-title">Goal<span>DESK</span></div>
        <div class="card-desc">Tracke deine Ziele.</div>
        <div class="card-note">Zum Speichern der Ziele ist eine Anmeldung erforderlich.</div>
      </a>
    </div>
  </main>

  <footer>
    <div style="margin-bottom:8px;">
      <a href="https://www.wissen-und-werkzeug.de">wissen-und-werkzeug.de</a> · Werkzeuge für den Alltag
    </div>
    <div>
      <a href="https://wissen-und-werkzeug.de/impressum/">Impressum</a> · 
      <a href="https://datenschutz.wissen-und-werkzeug.de/">Datenschutz</a>
    </div>
  </footer>

</div>

<script>
  const root = document.documentElement;
  const track = document.getElementById('toggleTrack');
  const thumb = document.getElementById('toggleThumb');
  const label = document.getElementById('themeLabel');

  const saved = localStorage.getItem('wuw-theme') || 'dark';
  applyTheme(saved);

  function applyTheme(theme) {
    root.setAttribute('data-theme', theme);
    if (theme === 'light') {
      track.classList.add('active');
      thumb.textContent = '☀️';
      label.textContent = 'Hell';
    } else {
      track.classList.remove('active');
      thumb.textContent = '🌙';
      label.textContent = 'Dark';
    }
  }

  function toggleTheme() {
    const current = root.getAttribute('data-theme');
    const next = current === 'dark' ? 'light' : 'dark';
    localStorage.setItem('wuw-theme', next);
    applyTheme(next);
  }
</script>
</body>
</html>
```

---

## auth/callback.html – OAuth-Redirect

Diese Datei verarbeitet den Google OAuth-Redirect von Supabase. Sie liest den `next`-Parameter aus der URL und leitet den Nutzer nach erfolgreichem Login zur richtigen App weiter. Für den Nutzer ist sie unsichtbar (kurze Ladeanimation).

```html
<!DOCTYPE html>
<html lang="de" data-theme="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Weiterleitung...</title>
  <style>
    body {
      margin: 0;
      background: #020617;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      font-family: 'Inter', sans-serif;
      color: #7a91ab;
      font-size: 14px;
    }
  </style>
</head>
<body>
  <div>Weiterleitung…</div>
  <script>
    // Supabase liefert den Token im URL-Hash nach OAuth
    // Der `next`-Parameter im Query-String sagt uns, wohin wir weiterleiten sollen

    const params = new URLSearchParams(window.location.search);
    const next = params.get('next') || 'https://desk-suite.wissen-und-werkzeug.de';

    // Hash mit Access-Token an die Ziel-URL anhängen, damit Supabase ihn dort verarbeiten kann
    const hash = window.location.hash;
    window.location.replace(next + (hash || ''));
  </script>
</body>
</html>
```

---

## So wird GoalDESK den Login aufrufen

In GoalDESK muss die `redirectTo`-URL beim Google-Login auf die Callback-Seite zeigen und den `next`-Parameter mitgeben:

```js
await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: {
    redirectTo: 'https://desk-suite.wissen-und-werkzeug.de/auth/callback?next=https://goaldesk.wissen-und-werkzeug.de'
  }
});
```

---

## Supabase-Einstellungen (Patrick erledigt das selbst)

Nach dem Deployment müssen folgende Werte in Supabase gesetzt werden:

- **Site URL:** `https://desk-suite.wissen-und-werkzeug.de/auth/callback`
- **Redirect URLs (Whitelist):** `https://desk-suite.wissen-und-werkzeug.de/**`

---

## Deployment-Ablauf (zur Info)

1. GitHub-Repo `desk-landing` erstellen
2. `index.html` und `auth/callback.html` pushen
3. In Coolify: neue Static-Site aus dem Repo anlegen, Domain `desk-suite.wissen-und-werkzeug.de`
4. DNS-Eintrag bei IONOS: A-Record `desk-suite` → `23.88.62.226`
5. SSL wird automatisch von Traefik übernommen

---

*Wissen & Werkzeug · DESK-Suite Landing Page*
