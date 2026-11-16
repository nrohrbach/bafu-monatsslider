# bafu-monatsslider

Monatsslider (Januar bis Dezember, ohne Jahr) für die monatlichen Erosionsrisikokarten des BAFU für das Schweizer Dauergrünland. Eine einzelne statische HTML-Datei, kein Build-Schritt, kein Backend.

Die Karte läuft nativ in LV95 (EPSG:2056). Die 12 Cloud Optimized GeoTIFFs (COG) werden direkt im Browser kachelweise aus `data.geo.admin.ch` gestreamt. Ihre Adressen liest die Seite zur Laufzeit aus dem STAC-Katalog.

## Daten

| | |
|---|---|
| Datensatz | `ch.bafu.erosion-gruenland_bodenabtrag` (Monthly soil erosion risk maps for Swiss permanent grassland) |
| Inhalt | Durchschnittlicher Bodenabtrag pro Hektar und Monat, RUSLE, 100 m Auflösung |
| Einheit | t/(ha·Monat) |
| Zeitbezug | 12 Monatskarten (Jahresgang). Im STAC tragen alle Items dasselbe `datetime` (2019-04-01), die Monate stecken nur in der Item-ID (`…_jan`, `…_mrz`, …). Der Slider zeigt deshalb nur Monate. |
| Quelle | [STAC-Collection](https://data.geo.admin.ch/api/stac/v1/collections/ch.bafu.erosion-gruenland_bodenabtrag), [Browser](https://data.geo.admin.ch/browser/index.html#/collections/ch.bafu.erosion-gruenland_bodenabtrag) |
| Herausgeber | Bundesamt für Umwelt BAFU. Nutzungsbedingungen siehe Collection. |

## Verwendung

`index.html` auf einem beliebigen statischen Webserver ablegen, zum Beispiel GitHub Pages (Settings, Pages, Branch `main`, Ordner `/`). Die Seite ist danach unter `https://nrohrbach.github.io/bafu-monatsslider/` erreichbar. Lokal geöffnet (`file://`) funktioniert sie in der Regel auch.

Bedienung:

- Slider, Pfeiltasten links/rechts oder ◀ ▶| wechseln den Monat, Leertaste oder ▶ spielt ab.
- Der Wert unter dem Mauszeiger (aus dem COG, volle Auflösung) erscheint oben rechts.
- «≥ … t/(ha·Monat)» legt die Obergrenze der Farbskala fest, «Auto» setzt sie zurück. Die Skala ist für alle 12 Monate gleich, damit sich die Monate vergleichen lassen.
- «Nullwerte ausblenden» macht Pixel mit dem Wert 0 durchsichtig.

## Funktionsweise

- **Projektion:** MapLibre GL JS 6.17 mit `addProjection` für EPSG:2056. Das 512-px-Kachelraster beginnt bei der Kachel 0/0/0 mit 2560 m/px und dem swisstopo-Ursprung (2 420 000 / 1 350 000). COGs und Hintergrund werden nicht umprojiziert.
- **Streaming:** Ein eigenes Protokoll (`erosion://`) zeichnet jede Kachel. Es wählt die gröbste COG-Overview, die die Pixelgrösse noch auflöst, liest nur das Fenster der Kachel (geotiff.js, HTTP-Range-Requests) und färbt sie ein. Dekodierte COG-Kacheln liegen in einem Cache.
- **Monatswechsel:** Jeder Monat ist eine eigene Ebene. Die neue Ebene liegt obenauf, die alte wird erst ausgeblendet, wenn die neue vollständig geladen ist.
- **STAC:** Die Items der Collection werden geladen, das COG-Asset (`image/tiff`) jedes Items wird über das Monatskürzel der Item-ID zugeordnet. Ist der STAC nicht erreichbar oder fehlt ein Monat, nimmt die Seite die Standard-URL `…/erosion-gruenland_bodenabtrag_<mon>/erosion-gruenland_bodenabtrag_<mon>_2056.tif` und meldet das.
- **Farbskala:** Grün, Gelb, Rot für niedriges, mittleres und hohes Risiko. Die automatische Obergrenze ist das 98. Perzentil aller positiven Werte, geschätzt aus den gröbsten Overviews der 12 COGs.
- **Hintergrund:** swisstopo Pixelkarte grau (WMTS in LV95), in das Kachelraster der Karte gelegt.

## Konfiguration

Vor dem Modul-Skript kann `window.EROSION_CONFIG` gesetzt werden, zum Beispiel für eigene Kopien der COGs oder für ein Testsystem:

```html
<script>
  window.EROSION_CONFIG = {
    stacItems: "https://…/collections/…/items?limit=100",   // STAC-Items der Collection
    fallbackBase: "https://…/ch.bafu.erosion-gruenland_bodenabtrag/",   // Standard-URLs, wenn der STAC fehlt
    maplibre: "https://…/maplibre-gl.mjs",
    maplibreWorker: "https://…/maplibre-gl-worker.mjs",
    wmtsBase: "https://…/2056/"                              // WMTS-Basis, danach {z}/{x}/{y}.jpeg
  };
</script>
```

Bibliotheken (über jsDelivr geladen): MapLibre GL JS 6.17.0, proj4 2.22.0, geotiff.js 2.1.3. Für den Betrieb ohne externes CDN lassen sich die Dateien selbst hosten und die URLs anpassen.

## Status und bekannte Punkte

- Getestet nur mit synthetischen COGs (headless Chrome, lokaler Server mit CORS und Range-Support). Gegen die echten BAFU-COGs und den echten STAC ist die Seite noch nicht geprüft.
- Zu prüfen beim ersten Lauf: CORS und Range-Freigabe von `data.geo.admin.ch` für die Seitenadresse, die COG-Kompression (geotiff.js 2.1.3 liest die gängigen Verfahren), die Bedeutung von 0 gegenüber Nodata ausserhalb des Dauergrünlands und die automatische Obergrenze der Farbskala.
- Die Monate sind Jahresgang-Karten. Die Jahreszahl 2019 im STAC-`datetime` ist kein Zeitbezug der Daten, sondern ein Platzhalter des Katalogs.

## Ordner

- `index.html`: aktuelle Version (natives LV95, kachelweises Streaming, STAC).
- `archiv/bodenabtrag-slider-v1.html`: erste Version. Liest pro Monat eine ganze Overview und projiziert sie einmal nach Web-Mercator um. Hardcodierte URLs, kein STAC. Nur zum Vergleich.
- `NOTICE.md`: Hinweise zu übernommenem Code.

## Lizenz

Für den eigenen Code ist noch keine Lizenz festgelegt. Übernommener Code aus dem swisseo-browser steht unter der MIT-Lizenz, siehe `NOTICE.md`.
