# Lernvisualisierungen

Interaktive Lerneinheiten der Brandeis Consulting GmbH, gehostet über GitHub Pages
und einbettbar in das Lernportal.

## Aufbau

```
index.html              Übersichtsseite, verlinkt alle Lerneinheiten
assets/base.css         Gestaltung nur der Übersichtsseite
slicing-dicing/         eine Lerneinheit je Ordner
  index.html
.nojekyll               schaltet die Jekyll-Verarbeitung ab
.github/workflows/      Deployment nach GitHub Pages
```

Jede Lerneinheit ist eine **eigenständige Einzeldatei**: HTML, CSS und JavaScript
in einem Dokument, Schriften aus dem System, kein Nachladen von fremden Servern.
Das ist Absicht. So lässt sich dieselbe Datei ohne Anpassung auch per iframe
einbetten, offline vorführen oder in ein anderes LMS kopieren.

## Neue Lerneinheit anlegen

1. Ordner mit sprechendem Namen anlegen, zum Beispiel `cds-assoziationen/`.
2. Die HTML-Datei darin als `index.html` ablegen. Dann ist die URL
   `.../cds-assoziationen/` ohne Dateiendung.
3. In `index.html` im Wurzelverzeichnis einen `article.item`-Block ergänzen.
4. Committen und pushen. Der Workflow veröffentlicht automatisch.

## Einmalige Einrichtung auf GitHub

Repository anlegen, dann unter **Settings → Pages** bei *Source* den Eintrag
**GitHub Actions** wählen. Danach genügt ein Push auf `main`.

Wer ohne Workflow auskommen möchte, wählt dort stattdessen
*Deploy from a branch*, Branch `main`, Ordner `/ (root)`, und löscht
`.github/workflows/pages.yml`. Für rein statische Dateien reicht das.

## Einbettung in das Lernportal

GitHub Pages setzt keinen `X-Frame-Options`-Header, die Seiten lassen sich also
ohne Weiteres per iframe einbinden. Jede Lerneinheit meldet ihre Höhe per
`postMessage` an die einbettende Seite, damit dort kein zweiter Scrollbalken
entsteht.

```html
<iframe id="viz-slicing-dicing"
        src="https://BENUTZER.github.io/lernvisualisierungen/slicing-dicing/"
        style="width:100%;border:1px solid #d6cfc2;height:2400px"
        loading="lazy" title="Slicing und Dicing"></iframe>
<script>
addEventListener("message", function (e) {
  if (e.origin !== "https://BENUTZER.github.io") return;
  if (e.data && e.data.type === "brandeis:height") {
    document.getElementById("viz-slicing-dicing").style.height = e.data.height + "px";
  }
});
</script>
```

Die Prüfung auf `e.origin` gehört dazu. Ohne sie könnte jede beliebige
eingebettete Seite die Höhe des iframes verändern.

## Eigene Domain

Soll statt `BENUTZER.github.io/lernvisualisierungen/` eine eigene Adresse wie
`lernen.brandeis.de` verwendet werden: eine Datei `CNAME` mit genau dieser
Adresse als einzigem Inhalt im Wurzelverzeichnis anlegen und im DNS einen
CNAME-Eintrag auf `BENUTZER.github.io` setzen. In den Pages-Einstellungen
anschließend *Enforce HTTPS* aktivieren. Beim Wechsel der Domain muss die
`e.origin`-Prüfung im Einbettungscode mitgezogen werden.

## Lizenz und Inhalte

Die Beispieldaten in den Lerneinheiten sind frei erfunden. Vor dem Umstellen des
Repositories auf öffentlich prüfen, ob alle enthaltenen Zahlen und Bezeichnungen
tatsächlich unbedenklich sind.
