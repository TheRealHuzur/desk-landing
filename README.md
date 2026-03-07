# desk-landing

Zentraler Einstiegspunkt der **DESK-Suite** – selbst gehostete Open-Source Tools auf einem Hetzner VPS.

🌐 **Live:** [desk-suite.wissen-und-werkzeug.de](https://desk-suite.wissen-und-werkzeug.de)

---

## Zweck

Diese Landing Page erfüllt drei Aufgaben:

1. **Übersichtsseite** aller DESK-Suite Tools
2. **Supabase Site URL** – zentraler OAuth-Redirect-Endpunkt
3. **auth/callback** – verarbeitet Google OAuth-Redirects und leitet zur jeweiligen App weiter

---

## Repo-Struktur

```
desk-landing/
├── index.html          # Landing Page
└── auth/
    └── callback.html   # OAuth-Redirect-Handler
```

---

## Tech-Stack

- Reines HTML, CSS, Vanilla JS – kein Framework, kein Build-Prozess
- Schrift: Inter (Google Fonts)
- Design: WUW Design System (Dark/Light Theme)
- Deployment: Coolify → Static Site
- SSL: Let's Encrypt via Traefik (automatisch)

---

## Deployment

Die Seite wird als **Static Site** über [Coolify](https://coolify.io) deployed.  
Ein manueller Redeploy in Coolify ist nach jedem Push erforderlich.

**Server:** `desk-app-01` · Hetzner Cloud · `23.88.62.226`  
**Subdomain:** `desk-suite.wissen-und-werkzeug.de` → A-Record auf `23.88.62.226`

---

## OAuth-Callback

`auth/callback.html` liest den `next`-Parameter aus der URL und leitet den Nutzer nach erfolgreichem Google-Login zur richtigen App weiter.

Jede App ruft den Login so auf:

```js
redirectTo: 'https://desk-suite.wissen-und-werkzeug.de/auth/callback?next=https://[appname].wissen-und-werkzeug.de'
```

### Supabase-Einstellungen

| Feld | Wert |
|---|---|
| Site URL | `https://desk-suite.wissen-und-werkzeug.de/auth/callback` |
| Redirect Whitelist | `https://desk-suite.wissen-und-werkzeug.de/**` |

---

## Neue App hinzufügen

Eine neue App-Karte in `index.html` innerhalb von `<div class="app-grid">` ergänzen:

```html
<a href="https://[appname].wissen-und-werkzeug.de" class="card card-clickable">
  <div class="card-title">[Erster Teil]<span>[Zweiter Teil]</span></div>
  <div class="card-desc">[Kurzbeschreibung]</div>
  <div class="card-note">[Optionaler Hinweistext]</div>
</a>
```

Außerdem in Supabase → Authentication → URL Configuration die neue Redirect URL zur Whitelist hinzufügen:

```
https://[appname].wissen-und-werkzeug.de/**
```

---

## DESK-Suite Apps

| App | URL | Status |
|---|---|---|
| GoalDESK | [goaldesk.wissen-und-werkzeug.de](https://goaldesk.wissen-und-werkzeug.de) | ✅ Live |

---

*Wissen & Werkzeug · [wissen-und-werkzeug.de](https://wissen-und-werkzeug.de)*
