# Hinweise zu übernommenem Code

`index.html` enthält angepasste Teile des swisseo-browsers
(<https://github.com/swisstopo/topo-swisseobrowser>): die Definition von EPSG:2056 als MapLibre-Projektion samt Kachelraster, das Kachelprotokoll, den COG-Zugriff mit Pyramide und Window-Reads, den Cache für dekodierte COG-Kacheln sowie das Zusammensetzen der swisstopo-WMTS-Kacheln.

Der swisseo-browser steht unter folgender Lizenz:

```
MIT License

Copyright (c) 2023 eu-cdse

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

Zur Laufzeit werden folgende Bibliotheken von jsDelivr geladen: MapLibre GL JS 6.17.0 (BSD-3-Clause), proj4 2.22.0 (MIT) und geotiff.js 2.1.3 (MIT).
