# BUZZER

Reaktionsspiel: Timer läuft ab → GO → der schnellste Tap holt den Pot.
Schwarz/lila, mobile-first. Spielgeld (Phase 1), noch kein echtes Geld.

**Repo:** https://github.com/august-schoen/buzzer-demo

## Struktur

| Datei | Zweck |
|---|---|
| `index.html` | Die komplette App (eine Datei). Läuft **offline** als Simulation und **online** gegen den Server — erkennt das selbst (grüner/grauer Punkt im Header). |
| `server/server.js` | Phase-1-Backend: Konten, Guthaben, echte private Lobbys mit Einladungslink, zentrale Reaktions-Wertung. Node.js + `ws`, keine weiteren Abhängigkeiten. |
| `server/test-lobby.js` | Automatischer 2-Spieler-Test (`node test-lobby.js`). |
| `server/join-as.js` | Test-Freund: `node join-as.js CODE Name 180` tritt einer Lobby bei und tippt beim GO. |
| `start-buzzer.bat` | Doppelklick auf Windows: startet Server + öffentlichen Tunnel. |
| `PROJEKT-NOTIZEN.md` | **Übergabe-Doku**: alle Produktentscheidungen, Stand, offene Fragen. Zuerst lesen. |

## Lokal starten

```
cd server
npm install        # einmalig
node server.js     # läuft auf http://localhost:8787
```

App im Browser: `http://localhost:8787` — der grüne Punkt am Logo zeigt die Server-Verbindung.

**Öffentlich testen (ohne Hosting-Account):**
```
cloudflared tunnel --url http://localhost:8787
```
gibt eine `https://…trycloudflare.com`-Adresse aus — funktioniert auf jedem Handy, solange der PC läuft. Adresse wechselt bei jedem Start. Unter Windows liegt cloudflared nach `winget install Cloudflare.cloudflared` in `C:\Program Files (x86)\cloudflared\`.

## Dauerhaft hosten

Render / Railway / Fly.io / beliebiger Node-Host:
- Build: `cd server && npm install`
- Start: `node server/server.js`
- Der Server respektiert die `PORT`-Umgebungsvariable und liefert die App selbst aus — eine URL für alles.

GitHub Pages (https://august-schoen.github.io/buzzer-demo/) kann keinen Server ausführen und zeigt daher die Offline-Simulation.

## Echter E-Mail-Code (optional, 2 Minuten)

Ohne Konfiguration zeigt die App den Bestätigungscode an (Demo-Fallback). Für echten Versand:

1. Kostenlosen Account bei **Brevo** (brevo.com, 300 Mails/Tag frei) oder **Resend** (resend.com) anlegen und einen API-Key erzeugen.
2. `server/mail-config.example.json` zu `server/mail-config.json` kopieren, Key + Absender-Adresse eintragen.
3. Server neu starten. Fertig — Codes kommen jetzt per Mail, geprüft wird serverseitig.

`mail-config.json` ist gitignored — der Key landet nie im Repo.

## Wo liegen welche Daten?

- **Code:** dieses Repo (Quelle der Wahrheit).
- **Konten, Guthaben & verifizierte E-Mails (online):** `server/data.json` auf dem Rechner/Host, auf dem der Server läuft. Bewusst nicht im Repo.
- **Auf dem Gerät (localStorage):** Spielstand der Offline-Simulation und das Login-Token.
- **Attrappen (bewusst):** Ausweis-/Alters-Check ist simuliert, Bezahl-Buttons sind reine Optik. E-Mail-Code ist echt, sobald `mail-config.json` existiert — sonst Demo-Fallback.
