# AI-DTL – AI Disclosure & Transparency Label

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24101111.svg)](https://doi.org/10.5281/zenodo.24101111)

Editor und Erklärseite für das AI-DTL, eine Selbstauskunft zur KI-Nutzung an einem Werk.
Ein Label besteht aus einem **Klartextsatz** und einem **Code**, z. B.:

```
Enthält mit KI erzeugte Inhalte. KI-Ausgaben von einem Menschen inhaltlich geprüft. Eine Person übernimmt die Verantwortung.
AI-DTL 0.5: G/H:C;R:H;Acc:I (GitHub Copilot, 10/2026)
```

- Live: <https://ai-dtl.github.io/> (Version 0.5 als Vorschau; die Freigabe folgt nach der Validierungsstudie)
- Lizenz: Code MIT, Inhalte CC BY 4.0 (siehe [Lizenz](#lizenz)) · DOI: [10.5281/zenodo.24101111](https://doi.org/10.5281/zenodo.24101111) · Kontakt: Joshua Dohmen, KI-Beauftragter Universität Koblenz
- Feedback: <https://github.com/ai-dtl/ai-dtl.github.io/issues>

Die Seite ist bewusst **statisch**: kein Build-Schritt, keine Abhängigkeiten, keine Telemetrie,
keine Speicherung, keine externen Requests. Es werden nur Dateien aus diesem Repo geladen; als Schrift
dient Open Sans, falls auf dem Gerät installiert, sonst Arial. Ausgeliefert wird über GitHub Pages
aus dem Branch `main`.

## Struktur

```
index.html            Editor 0.5 (Formularelemente, Studienmodus)
label/index.html      Erklärseite: 0.5-Labels, ältere 0.4-Links mit Hinweis; ohne Parameter
                      Übersicht aller Stufen mit Grenzfällen (Ziel von „Mehr zu den Stufen“)
changelog/            Versionsgeschichte
css/style.css         Gestaltung (keine Webfonts: Open Sans, falls installiert, sonst Arial)
js/
  spec.js             SITE_BASE, ENABLE_M und SPEC-Registry: Codes, Regeln, alle Texte DE/EN
  label-core.js       reine Logik ohne DOM (Editor, Erklärseite, Node-Tests)
  editor.js           Editor
  icons.js            Piktogramme und Badge
  services.js         Dienste und Modelle mit Host-Vorschlägen
  examples.js         Beispielkatalog (Anhang D)
tests/                Node-Tests inkl. Export-Snapshots
rc/                   nur noch Weiterleitungen: rc/ → Hauptseite, rc/label/ → label/
                      (Parameter bleiben erhalten, alte Vorschau- und Studienlinks funktionieren)
```

## Tests ausführen

Node.js ≥ 20, keine Installation von Paketen nötig:

```bash
npm test
```

`node --test` findet alle `*.test.js` in `tests/`.

Die Export-Snapshots (`tests/snapshots/exports.json`) halten die erwarteten Ausgaben für die
sechs Referenzzustände fest. Nach einer gewollten Textänderung neu erzeugen und prüfen:

```bash
UPDATE_SNAPSHOTS=1 node --test tests/exports.test.js
```

Die Seiten selbst lassen sich mit jedem statischen Server ansehen, z. B. `py -m http.server 3456`
im Repo-Ordner und dann `http://localhost:3456/`. Lokal erzeugte Links zeigen auf den lokalen
Server, auf GitHub Pages auf `SITE_BASE`.

## Texte und Regeln pflegen (`js/spec.js`)

- Alle sichtbaren Texte stehen nur in `spec.js` (`SPEC[version].i18n.de/en`). Editor, Erklärseite,
  Satz, Sprechtext, Badge-Wörter, Hinweise und die Liste „Wann gibt der Editor Hinweise?“ lesen von dort.
- `SPEC['0.4']` ist eingefroren (Altlinks). Änderungen nur in `SPEC['0.5']`.
- `ENABLE_M` schaltet die vorläufige Stufe M. Bei `false` greifen die Texte „ohne M“, M verschwindet
  aus Grammatik, Editor und Entscheidungshilfe; Beispiel D.12 wird G.
- Hinweisregeln stehen in `SPEC[v].warnings` (Bedingung + Auslöser), die Texte in `i18n.*.warn`.
- Nach Änderungen: `npm test`. Bei Textänderungen, die Exporte betreffen, Snapshots neu erzeugen.

## Dienste-Liste pflegen (`js/services.js`)

- Ins Label kommt nur `name`. `hint` erscheint nur in der Vorschlagsliste.
- `host` ist ein **Vorschlag** für den Hosting-Schritt. Jeden Eintrag mit `host` gegen die Verträge
  der Universität Koblenz prüfen. Einträge ohne `host` bekommen keinen Vorschlag.
- `generic: true` setzt `Name/` ein und fragt nach dem Modell.
- Die Modellnamen veralten schnell und werden von Hand gepflegt; es wird nichts nachgeladen.

## Studienmodus

`https://ai-dtl.github.io/?modus=studie` (optional `&fall=V07`, nur Format `V` + zwei Ziffern).
Ältere Studienlinks auf `…/rc/?modus=studie…` werden mit allen Parametern weitergeleitet.

- Banner „Studienmodus: Ihre Eingaben werden nicht gespeichert“ (mit Fallnummer)
- „Beispiele ansehen“ ausgeblendet, Host-Vorschlag aus Werkzeugen abgeschaltet
- großer Button „Ergebnis für den Fragebogen kopieren“: Code und Link in einer Zeile, durch ein Leerzeichen getrennt
- „Neu beginnen“ hervorgehoben
- keine Speicherung, kein Logging, kein `localStorage`

## Label laden und aktualisieren

Der Editor nimmt dieselben Parameter wie die Erklärseite: `https://ai-dtl.github.io/?s=G&h=H%3AC&…`
füllt das Formular. 0.4-Links (ohne `v`) werden übernommen, mit dem Hinweis, die Angaben nach 0.5 zu prüfen.

## Prüfliste Barrierefreiheit (manuell, Anhang E.6)

Mit NVDA (Firefox/Chrome) oder VoiceOver (Safari) auf der Hauptseite:

1. **Nur Tastatur**: Tab, Umschalt+Tab, Leertaste, Pfeiltasten. Für jedes Beispiel aus „Beispiele ansehen“
   das Label einmal von Hand nachbauen. Fokus ist immer sichtbar (roter Rahmen).
2. **Schritte**: Die Legende jedes Schritts wird mit Nummer vorgelesen („Schritt 2 Tool-Angabe“).
   Bei „No AI“ springen die Nummern lückenlos von 1 auf 2.
3. **Optionen**: Name (z. B. „Generative G“), Beschreibung, beim Ausmaß die Gruppe („Kein KI-Inhalt im Werk“ / „KI-Inhalt im Werk“) und Zustand (aktiviert/nicht aktiviert,
   Kontrollkästchen vs. Optionsfeld) werden angesagt.
4. **Werkzeug**: Das Feld wird als Kombinationsfeld angesagt; Pfeil runter öffnet die Vorschläge,
   der aktive Vorschlag wird vorgelesen, Enter übernimmt. Blockierte Zeichen ; ( ) , | lösen einen
   gesprochenen Hinweis aus. Monat und Jahr sind als „Monat der letzten Nutzung“ gruppiert.
5. **Rückmeldungen**: „Im Label: …“ unter Schritt 1 und 3 (bei zwei oder mehr gewählten Optionen), „Unsicher?“-Hilfen (Fokus springt auf die erste Frage), Host-Vorschlag, Hinweise und
   „Es fehlt noch: …“ werden ohne Fokuswechsel angesagt (Live-Regionen).
6. **Ergebnis**: Satz, Code und Badge-Beschreibung (Bild mit Titel = Code, Beschreibung = Satz)
   werden vorgelesen. Kopieren sagt „Kopiert: …“ an.
7. **Zoom 200 %** und Fensterbreite 320 px: kein waagerechtes Scrollen der Seite.
8. **Kontrast**: alle Textfarben ≥ 4,5:1 (Grau `#6e6e6e` auf `#f5f5f5` = 4,68:1).
9. **Sprache**: Nach Umschalten auf EN trägt `<html>` `lang="en"`; die Ansage wechselt die Aussprache.

## Zitieren

Dohmen, J. (2026). *AI-DTL – AI Disclosure & Transparency Label* [Computer software]. Zenodo.
<https://doi.org/10.5281/zenodo.24101111>

Die DOI oben (Konzept-DOI) zeigt immer auf die neueste archivierte Version. Einzelne Versionen haben
eigene DOIs, z. B. 0.5.0-preview: [10.5281/zenodo.24101112](https://doi.org/10.5281/zenodo.24101112).
Neue Versionen entstehen über ein GitHub-Release; Zenodo übernimmt sie automatisch und liest die
Metadaten aus `.zenodo.json`. Zitierangaben für GitHub stehen in `CITATION.cff`.

## Lizenz

- **Code** (HTML, CSS, JavaScript, Tests): MIT, siehe [`LICENSE`](LICENSE).
- **Inhalte** (Spezifikation und Texte in `js/spec.js`, Piktogramme, Changelog, Dokumentation):
  [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/), siehe [`LICENSE-CONTENT.md`](LICENSE-CONTENT.md).
