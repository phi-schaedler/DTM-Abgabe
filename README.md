# | DTM | Abschlussaufgabe | SOSE25 |
## Impressum
Autor: Philipp Schädler
Herausgeber: BHT  
Kontakt: phsc3459@bht-berlin.de & philipp.schaedler@gmail.com

<br><br><br>
# Inhaltsverzeichnis
[EP.01 Dasymetrische Choroplethenkarte](#EP.01)<br>
[EP.02 Gitterchoroplethenkarte](#EP.02)<br>
[EP.03 Punktrasterkarte](#EP.03)<br>
[EP.04 Value-by-Alpha](#EP.04)<br>
[EP.05 Tilemap](#EP.05)<br>
[EP.06 Flowmap](#EP.06)<br>
[EP.07 Mesh-Daten](#EP.07)<br>
[EP.08 Animationen in QGIS](#EP.08)<br>
[EP.09 3D-Gebäudemodelle](#EP.09)<br>

<br><br>
<a id="EP.01"></a>
<br>
# EP.01 | Dasymetrische Choroplethenkarte | Bevölkerungsdichte Berlin |
![image](https://github.com/phi-schaedler/B10-DTM/blob/b3a8addf33c9140a84596192b511bf04713ea80a/Files/Schaedler_Philipp_Arbeitsaufgabe_01.png)
## Ergebnis
Die Karte im A3 Format umfasst zwei ähnliche Darstellungen der Bevölkerungsdichte in Berlins nach Planungsräumen (LOR):
* Einfache Choroplethenkarte – Bevölkerungszahl gemessen auf die Gesamtfläche 
* Dasymetrische Choroplethenkarte – zugeschnittene Bevölkerungszahl auf tatsächlich bewohntem Gebiet
## Arbeitsschritte
1. __Datenbasis__
* Planungsräume (LOR 2021) als Shapefile von der Senatsverwaltung [downloaden](https://www.berlin.de/sen/sbw/stadtdaten/stadtwissen/sozialraumorientierte-planungsgrundlagen/lebensweltlich-orientierte-raeume/).
* Einwohnerzahlen [downloaden](https://www.berlin.de/sen/sbw/stadtdaten/stadtwissen/monitoring-soziale-stadtentwicklung/bericht-2021/tabellen/=).
* Einwohnerzahlen (CSV, Stand Januar 2023) bereinigen (Kopfzeilen löschen, nur Einwohner-Spalten behalten, 1000er-Trennpunkte entfernen) und als Ganzzahlen importieren.
2. __Join__
* PLR-ID als Ganzzahlspalte im Shapefile anlegen.
* CSV und Geometrien per Attribute nach Feldwert verknüpfen.
3. __Einfache Choroplethenkarte__
* Bevölkerungsdichte berechnen
`"EW" / ($area / 10000)` → Einwohner je Hektar
Symbolisierung: abgestuft, Klassifizierung nach Jenks, Farbverlauf.
4. __Dasymetrische Choroplethenkarte__
* Corine Land Cover Daten (CLC) [downloaden](https://land.copernicus.eu/en/technical-library) und auf urbane Codes 111 und 112 filtern.
* Mit _Auflösen_ zusammenführen → Urban Fabric.
* _Verschneidung_ mit LOR-Einwohnern.
* Dichte nur bezogen auf urbane Fläche berechnen.
* Extremwerte prüfen und ggf. manuell löschen.
* Symbolisierung analog zur einfachen Karte; Zusatzfeld unbewohnte Fläche.
## Vorteile der Methode
* Nur bewohnte Flächen berücksichtigt, unbewohnte (Wälder, Parks, Wasser) ausgeschlossen.
* Hot Spots innerstädtischer Dichte treten klar hervor.
* Vergleichbarkeit: Kombination von administrativen Einheiten mit realer Flächennutzung.
* Bessere Grundlage für Infrastruktur- und Wohnraumbedarfsanalysen.
## Nachteile der Methode
* Datenaufwendig: Mehrere Quellen (LOR, Einwohnerdaten, CLC) und viele Verarbeitungsschritte.
* Datenqualität entscheidend: Genauigkeit hängt von CLC-Daten ab; Schnittfehler oder Ausreißer möglich.
* Vereinfachende Annahme: „Keine Bewohner außerhalb urbaner Flächen“ – in ländlichen Kontexten problematisch.
* Komplexität: Höherer Zeit- und Rechenaufwand, weniger intuitiv für Laien als einfache Choroplethenkarten.

<br><br>
<a id="EP.02"></a>
<br>
# EP.02 | Gitterchoroplethenkarte | Kirschbäume Berlins |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_02.png)
## Ergebnis
Die Hexagon Choroplethenkarte (Seitenlänge = 500 m) zeigt die Verteilung von Kirschbäumen in Berlin. Einige Hotspots fallen auf. Am ehemaligen Grenzstreifen zwischen Ost- und Westberlin sind viele Kirschbäume gepflanzt. Die Verteilung ist ungleichmäßig.
## Arbeitsschritte
1. __Daten beschaffen & laden__
* Bezirksgrenzen und Baumbestand (Straßen- und Anlagenbäume) [downloaden](https://fbinter.stadt-berlin.de/fb/index.jsp).
* Stadtgrenze als Clip-Geometrie bereitstellen.
2. __Baumarten filtern__
* In beiden Baum-Layern Kirschbäume selektieren, z. B. per Ausdruck.
`art_dtsch ILIKE '%kirsche%'`
* Nur ausgewählte Bäume als eigener Punktlayer exportieren.
3. __Hexagon-Gitter erzeugen__
* _Vektor_ → _Recherchewerkzeuge_ → __Gitter erzeugen_: Typ = Hexagon (Polygone), Seitenlänge __500 m__ (Achtung: Meter als Einheit!).
* Gitter auf Berlin-Ausdehnung erstellen und (empfohlen) mit Stadtgrenze zuschneiden, damit Randhexagone nicht überstehen.
4. __Räumliche Aggregation__
* _Attribute nach Position verknüpfen (Zusammenfassung)_: Ziel = Hexagon-Grid, Join = Kirschbaum-Punkte, Anzahl der Bäume pro Zelle berechnen.
5. __Symbolisierung__
* Abgestuft nach Baumanzahl; sinnvolle Klassenbildung (z. B. Jenks) und klare, eindimensionale Farbrampe (hell → dunkel).
* Optional: separate Karten/Layer für Baumhöhen oder Unterarten, aber nicht in dieser Karte mischen (Lesbarkeit).
6. __Layout & Kontext__
* Titel, Datenquelle, Legende; "dezenter" Hintergrund.
* Orientierungshilfen (Bahnhöfe) als Referenz-Overlays.
## Vorteile der Methode
* Gleich große Hexagone eliminieren Verwaltungsgrenzen-Bias; Muster und Cluster werden schnell sichtbar.
* Regelmäßiges Gitter wirkt aufgeräumt und ästhetisch; Lücken/Leerräume werden transparent.
* Zellgröße lässt sich an Fragestellung anpassen (Detailgrad ↔ Stabilität).
* Vollständig mit QGIS-Bordmitteln (WFS, Gitter, räumliche Zusammenfassung) umsetzbar.
## Nachteile der Methode
* Orientierung: Ohne bekannte Grenzen/POIs fällt Laien die Verortung schwer; Kontextlayer helfen.
* Rand- und Clipping-Effekte: Ungeschnittene Randhexagone können außerhalb Berlins wirken; unbedingt auf Stadtgrenze clippen.
* Modifizierbarkeit der Ergebnisse: Wahl von Zellgröße und Klassifizierung (z. B. Jenks) beeinflusst Musterwahrnehmung.
* Generalisation: Innerhalb einer 500-m-Zelle werden lokale Unterschiede gemittelt; Detailanalysen sind begrenzt.

<br><br>
<a id="EP.03"></a>
<br>
# EP.03 | Punktrasterkarten | AirBnB Berlin |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_03.png)
## Ergebnis
Die Punktrasterkarte ermöglicht eine angepasste Rasteransicht, in diesem Fall mit 5 Variabeln. Es wird der Preis und die Anzahl an unterschiedlichen Unterkünfte dargestellt. Insbesondere in den Innenstadtbezirken häuft sich Unterkunftsanzahl. Die Preise sind zum Teil unabhängig davon und richten sich möglicherweise eher nach der Lage innerhalb der Stadt (angesagte und unbeliebte Bezirke). 
## Arbeitsschritte
1. __Datenbeschaffung & Import__
* Airbnb-Daten (_listings.csv_) von __InsideAirbnb__ [downloaden](https://insideairbnb.com/)
* CSV in QGIS als Punktlayer importieren (Koordinatenfelder definieren).
* Bezirksgrenzen über WFS-Verbindung aus dem Geoportal Berlin laden.
2. __Datenaufbereitung__
* Listings nach „room_type“ (Entire home, Private room, Shared room, Hotel room) klassifizieren.
* Neue Spalten anlegen (Boolean-Felder für jeden Typ oder Summen/Anteile).
* NULL-Werte und Ausreißer (z. B. Extrempreise) bereinigen.
3. __Rastererstellung & Spatial Join__
* Regelmäßiges Gitter (in diesem Fall 2 km Kantenlänge) über Berlin erzeugen.
* _Mit Attribute nach Position verknüpfen (Zusammenfassung)_: Anzahl und Durchschnittspreise pro Rasterzelle berechnen.
4. __Darstellungsvarianten__
* Mehrdimensionale Punktrasterkarte (hier dargestellt):
* Punktfarbe (großer Ring) = Durchschnittspreis, Punktfarbe (kleiner Ring) = Kategorie, Größe der Punkte = Anzahl der Objekte nach Art, Versatz = Objektart.
* Oder mit Airbnb-Logo ([SVG](https://svglogo.co/logo/airbnb/)) als Punktsymbol.
* Mehrschichtige Karte: Vier Layer übereinander mit je eigenem Raumtyp (verschobene Symbole in Rautenanordnung).
## Vorteile der Methode
* Hohe Informationsdichte: Anzahl, Preis und Typen können in einer oder mehreren Karten gleichzeitig sichtbar gemacht werden.
* Vergleichbarkeit: Rasterzellen haben gleiche Fläche → Muster unabhängig von Verwaltungsgrenzen erkennbar.
* Hot-Spot-Erkennung: Klare räumliche Muster (z. B. viele teure Angebote in Mitte, Friedrichshain, Kreuzberg, Prenzlauer Berg).
* Sowohl in einer Gesamtdarstellung als auch in getrennten thematischen Karten umsetzbar.
* Anschaulich für Publikum: Farbrampen, Punktsymbole oder Logos vermitteln Tendenzen intuitiv.
## Nachteile der Methode
* Detailverlust: Innerhalb einer Rasterzelle bleiben feine Unterschiede unsichtbar.
* Rasterzellen sind weniger vertraut als Bezirke → Orientierung schwieriger.
* Kombination von Größe, Farbe und Kategorie führt schnell zu visuellem Clutter in verdichteten Gebieten.
* Lesbarkeit & Vergleichbarkeit:
  * schwer, exakte Werte aus Punktgrößen abzuleiten.
  * hoher Platzbedarf, Klassengrenzen nicht direkt vergleichbar.
* Fehlinterpretationen möglich: Punkte könnten als exakte Standorte verstanden werden.

<br><br>
<a id="EP.04"></a>
<br>
# EP.04 | Value-by-Alpha | Bundestagswahl 2021/25 |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_04.png)
## Ergebnis
Mit Value-by-Alpha wird die Höhe eines Wertes durch Farbintensität ausgedrückt. In diesem Fall %-Stimmenanteil der Siegerpartei in der jeweiligen Bundestagswahl.
## Arbeitsschritte
1. __Datenbeschaffung__
* Wahlkreis-Shapefile (generalisiert) [downloaden](https://www.bundeswahlleiterin.de/bundestagswahlen/2025/wahlkreiseinteilung/downloads.html)
* endgültigen Wahlergebnisse [downloaden](https://bundeswahlleiterin.de/bundestagswahlen/2025/ergebnisse/opendata.html) (CSV/Kerg-Datei) von 2021 und 2025 
2. __Datenaufbereitung__
* In Excel irrelevante Spalten und Zeilen löschen, nur gültige Zweitstimmen der Parteien SPD, CDU/CSU, Grüne, Linke, AfD behalten.
* CSU-Werte in Bayern der CDU zurechnen.
* Als CSV exportieren, Wahlkreisnummer (_WKR_NR_) als Integer definieren.
3. __Datenverknüpfung__
* CSV und Wahlkreis-Geometrien in QGIS laden.
* Mit Attribute nach Feldwert verknüpfen über „WKR_NR“ joinen.
4. __Attributberechnung__
Neue Felder erstellen:
* _g_value__ → Stimmenzahl der siegreichen Partei
`array_max( array( "SPD" , "CDU" , "Gruenen" , "AFD" , "Linke"))`
* _g_name__ → Name der siegreichen Partei
`with_variable( 'maxVal', array_max( array( "SPD" , "CDU" , "Gruenen" , "AFD" , "Linke")), CASE WHEN "SPD" = @maxVal THEN 'SPD' WHEN "CDU" = @maxVal THEN 'CDU' WHEN "Gruenen" = @maxVal THEN 'Gruenen' WHEN "AFD" = @maxVal THEN 'AFD' WHEN "Linke" = @maxVal THEN 'Linke' END)`
* _g_proz_gueltig__ → Stimmenanteil in Prozent.
5. __Symbolisierung__
* Regelbasierte Darstellung: Jede Partei erhält ihre typische Parteifarbe.
* Value-by-Alpha Mapping: Transparenz wird proportional zum Stimmenanteil geregelt
`set_color_part('white', 'alpha', scale_linear("g_proz_gueltig", min("g_proz_gueltig"), max("g_proz_gueltig"), 200, 0))`
* Ergebnis: Farbton = Siegerpartei, Farbintensität = Stärke des Ergebnisses
5. __Darstellung & Vergleich__
* Karten für 2021 und 2025 nebeneinanderstellen, um Veränderungen sichtbar zu machen.
## Vorteile der Methode
* Zwei Variablen gleichzeitig: Partei (Farbton) + Stimmenstärke (Deckkraft) in einer Ebene.
* Deutlich erkennbar, wo Parteien mit großem oder kleinem Vorsprung gewonnen haben.
* Bekannte Parteifarben erleichtern das Verständnis ohne aufwendige Legende.
* Karten von 2021 und 2025 nebeneinander zeigen politische Verschiebungen klar.
* Alle Daten stammen von der Bundeswahlleiterin → konsistent und kompatibel.
## Nachteile der Methode
* Ähnliche Farbtöne (z. B. Rot der SPD vs. Rosa der Linken) sind schwer unterscheidbar → begrenzte Anzahl darstellbarer Parteien.
* Aufwendige Datenaufbereitung: Wahldaten müssen manuell bereinigt, umstrukturiert und neu formatiert werden (CSU/CDU-Zusammenführung).
* Fehleranfälligkeit: Kleine Unachtsamkeiten in der Excel-Bearbeitung können falsche Ergebnisse erzeugen
* Alpha-Skalierung: Bei knappen Ergebnissen zu schwach sichtbar, bei sehr hohen Anteilen zu dominant.
* Kleine Wahlkreise sind schlechter zu erkennen und vermitteln ein verzehrtes Bild (Berlin hat viele Einwohner aber nur wenig Fläche)

<br><br>
<a id="EP.05"></a>
<br>
# EP.05 | Tile-Maps | Verkehrsunfälle mit Personenschaden |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_05.png)
## Ergebnis
Die Tile Map vergleicht die Anzahl der Verkehrsunfälle mit Personenschaden zwischen den Bundesländern der BRD. In Norddeutschland sollte man besonders vorsichtig fahren. Erstaunlich ist die niedrige Unfallquote von Hessen als Verkehrsknotenpunkt.
## Arbeitsschritte
1. __Grundlage erstellen__
* _Mit Gitter erzeugen_ ein rechteckiges Raster über der ungefähren Ausdehnung Deutschlands anlegen (Seitenlänge beliebig, wird später angepasst).
* 16 Kacheln wählen, die die Lage der Bundesländer grob wiedergeben; Auswahl als neuen Layer speichern.
* Verkehrsdaten [downloaden](https://www.statistikportal.de/de/transport-und-verkehr/strassenverkehrsunfaelle)
2. __Geometrie anpassen__
* Alle ausgewählten Kacheln mit den _Erweiterten Digitalisierungswerkzeugen_ verschieben und skalieren, bis Form und Größe ungefähr Deutschland entsprechen (4×4-Raster).
* Bei Bedarf feintunen (Stadtstaaten liegen innerhalb anderer Länder → pragmatische Kompromisse).
3. __Attribute vorbereiten__
* Im Kachel-Layer eine Spalte für Länderkürzel (z. B. “BE”, “BW”…).
* In Excel/CSV die thematische Tabelle anlegen (Verkehrsdaten).
* Als CSV speichern und in QGIS als getrennte Textdatei (ohne Geometrie) laden.
4. __Join & Datenpflege__
* CSV über das Länderkürzel mit dem Kachel-Layer verknüpfen (_Attribute nach Feldwert verknüpfen_).
* zusätzlich ID-Spalte pflegen; unnötige Felder entfernen.
5. __Symbolisierung__
* _Abgestufte Darstellung_ auf die Kennzahl (Unfälle) legen geeignet sind Quartile; passenden Farbverlauf wählen.
* Beschriftung: Kürzel-Spalte als Text in der Kachelmitte.
* Quadratfüllung in Klassenfarbe
6. __Layout__
* Titel, Quellenangabe; Neutraler Hintergrund.
## Vorteile der Methode
* Vergleichbarkeit ohne Flächenbias: Alle Bundesländer gleich groß → Werte sind auf einen Blick vergleichbar; kleine Länder gehen nicht unter.
* Aufgeräumte, kompakte Darstellung; eignet sich für Nicht-Fachpublikum (Presse/Online).
* Erstellung ohne Zusatz-Plugins, lässt sich schnell für neue Themen wiederverwenden.
## Nachteile der Methode
* Tile Mapping – begrenzte Genauigkeit: Stadtstaaten wie Berlin oder Bremen lassen sich nur annähernd platzieren
* Einheitsgröße – Informationsverlust: Flächenbezogene Aspekte (z. B. Verkehrsnetz mit Unfallhotspots) gehen verloren

<br><br>
<a id="EP.06"></a>
<br>
# EP.06 | Flowmap | Flucht aus dem Südsudan |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_06.png)
## Ergebnis
Der Südsudan ist von inneren Unruhen erschüttert. Die Lebensgrundlage von vielen Menschen ist sehr unsicher. Deshalb sind mehere Millionen Menschen (insbesondere in die Nachbarländer) geflohen.
## Arbeitsschritte
1. __Datenbeschaffung & Aufbereitung__
  * UNHCR Flüchtlingszahlen [downladen](https://www.unhcr.org/refugee-statistics/download)
  * weltweite Landesgeometrien [downloaden](https://www.naturalearthdata.com/downloads/)
  * In Excel Pivot-Tabellen anlegen (Spalten = Jahr, Zeilen = Zielland-ISO, Werte = Summe) und als CSV exportieren
  * Tabelle in QGIS als Textlayer importieren (ohne Geometrie)
2. __Zentroid-Berechnung & Korrektur__
  * Für alle Länder Mittelpunkte (Zentroide) berechnen.
  * Überseegebiete ggf. manuell korrigieren, da Mittelpunkte im Wasser liegen können.
3. __Join & Liniengenerierung__
  * CSV-Daten über ISO-Code mit Zentroid-Layer verknüpfen.
  * Ursprungspunkt festlegen (z. B. Syrien, Deutschland oder Berlin).
  * Im Feldrechner X-/Y-Koordinaten für Ursprung und Ziel eintragen.
  * Mit dem Werkzeug XY → Linie Verbindungen erzeugen.
4. Projektion & Symbolisierung
  * Orthographische Projektion anlegen mit Fokus auf Juba (`+proj=ortho +lat_0=4.85 +lon_0=31.57 +x_0=0 +y_0=0 +a=6371000 +b=6371000 +units=m +no_defs`) für Globuseffekt.
  * Hintergrund dunkelblau mit Shapeburst-Füllung für Ozeandarstellung.
  * Linienbreite proportional zur Anzahl (Flüchtlinge oder Austauschplätze).
  * Ausgangsland/-stadt durch auffälligen Punkt hervorheben.
  * Zielpunkte zusätzlich skalieren, um Mengenunterschiede zu verdeutlichen.
## Vorteile der Methode
* Anschaulich & intuitiv: Richtung und Distanz zwischen Herkunft und Ziel werden klar erkennbar.
* Graduierte Linienbreiten und Symbolgrößen machen Mengenunterschiede sofort sichtbar.
* Orthographische Projektion wirkt globusartig und ansprechend.
* Mustererkennung: Hotspots und Bewegungsmuster lassen sich schnell identifizieren.
* Einsetzbar für verschiedene Themen (Flucht, Migration, Austauschprogramme).
## Nachteile der Methode
* Linienüberlagerung: Bei vielen ähnlichen Verbindungen wird die Karte unübersichtlich, Details können verdeckt werden.
* Ungenauigkeit durch Zentroide: Länder mit Überseegebieten führen oft zu falschen Mittelpunkten.
* Begrenzter Kartenausschnitt: Orthographische Projektion zeigt nur eine Halbkugel, andere Regionen werden abgeschnitten.
* Interpretationsschwierigkeiten: Für Personen mit wenig Länderkunde oder ohne Legende schwerer verständlich.
* Visuell statt metrisch: Karten sind eher für die Darstellung gedacht, nicht für präzise Analysen.
* Erstellungsaufwand: Daten filtern, Zentroide korrigieren und Projektionen definieren erfordert Zeit und Fachkenntnis.

<br><br>
<a id="EP.07"></a>
<br>
# EP.07 | Mesh-Daten | Orkan Daria |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_07.gif)
## Ergebnis
Ein GIF zeigt wie Orkan Daria Ende Januar 1990 über Westeuropa und Deutschland hinwegzieht. Es veranschaulicht die Bewegung des Windes über eine zeitliche Abfolge im Stundentakt. Im Frame sind Datum und Uhrzeit und Windvektoren aus einem GRIB Mesh Datensatz enthalten. 
## Arbeitsschritte
1. __Datenbeschaffung__
   * Verwaltungsgrenzen Deutschlands [herunterladen](https://www.naturalearthdata.com/downloads/)
   * GRIB Datensatz mit den Windgeschwindigkeiten vom 24.01.-26.01.1990. [herunterladen](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels?tab=download) (10m u-component of wind, 10m v-component of wind)
2. __Datenaufbereitung
   * Grenzen mit _Auflösen_ vereinfachen, Füllung transparent darstellen, Umrisse beibehalten
   * GRIB-Layer in QGIS importieren und Symbolisierung auf Vektor-Darstellung umstellen
3. Symbolisierung & Zeitsteuerung
   * Passendes Farbschema und Pfeildarstellung für Windrichtung und -stärke auswählen
   * Unter _Zeitlich_ die dynamische Zeitsteuerung aktivieren und den gewünschten Zeitbereich definieren.
4. __Zeitstempel & Kartenelemente__
  * Temporären Punktlayer anlegen, Symbolisierung transparent, Beschriftung mit: `format_date(@map_start_time, 'd. MMMM yyyy') || '\n' || format_date(@map_start_time, 'HH:mm')`
  * Maßstab, Titel, Impressum als Dekoration einfügen
5. __Animation & Export__
  * Mit dem Uhr-Symbol in QGIS die Sequenz abspielen, FPS und Tempo einstellen
  * Einzelbilder als PNG exportieren
  * Extern ([Photoshop](https://www.adobe.com/africa/learn/photoshop/web/make-animated-gif) oder Python-Skript) Bilder zu einem GIF zusammenfügen
## Vorteile der Methode
* Zeitliche Entwicklung des Sturms wird intuitiv vermittelt.
* Einfache Verständlichkeit: Auch Laien können Bewegungen und Intensität sofort nachvollziehen.
* Farben, Pfeile, Geschwindigkeit und Hintergrund frei wählbar.
* Besonders geeignet für Präsentationen, Medien und Online-Plattformen.
* Standardisierte Grundlage: GRIB-Daten bieten gleichmäßige räumliche Auflösung für Vergleiche.
## Nachteile der Methode
* Fehlende Quantifizierung: Exakte Werte (z. B. m/s) und detaillierte Legenden fehlen oft.
* Beschränkungen von GIFs:
  * Nicht interaktiv (kein Zoomen, Stoppen, Zurückspulen).
  * Reduzierte Qualität (Farbtiefe, Auflösung) durch Komprimierung.
  * Große Dateigröße bei längeren Sequenzen problematisch.
* Technische Hürden: GIF-Erstellung nicht direkt in QGIS möglich, externe Tools nötig.
* Große GRIB-Datenmengen beanspruchen Speicher und Leistung.
* Zellgrenzen können unnatürlich wirken und reale Strukturen zerschneiden.

<br><br>
<a id="EP.08"></a>
<br>
# EP.08 | Animationen in QGIS | Leoniden Sternschnuppen |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_08.png)
## Ergebnis
Eine statische Karte (PNG) zeigt alle in der Nacht vom 17./18. November 2024 erfassten Leoniniden Meteore über Deutschand und Umgebung – jede Bahn als farblich abgestufte Linie vom Eintrittspunkt bis zum Verglühen. Zur Visualisierung von Meteorschauern konnten offene Datenquellen genutzt werden. 
## Arbeitsschritte
1. __Datenbeschaffung__
    * Leoniden Messungen für den 17./18. 01 2025 als CSV von der öffentlichen Meteor-Map [herunterladen](https://tammojan.github.io/meteormap/)
    * Die Beobachtungsdaten (Startzeit, Koordinaten, ggf. Helligkeit) können damit in QGIS importiert werden
    * Informationen über unterschiedliche Metoritenschauer können sich [hier](https://www.meteorshowers.org/) visuell ansprechend betrachtet werden
2. __Datenaufbereitung__
    * Projektion auf _EPSG:4326_ setzen
    * Grenzen der BRD hinzufügen
    * Es ist auch möglich mit Python den Zeitstempel in ein Datetime-Format umzuwandeln (`"pd.to_datetime(...)"`)
3. __Geometrieerzeugung__ 
    * mit _Geometrie nach Ausdruck_ Linien aus Start  und Endkoordinate der Meteorspuren erzeugen
4. __Symbolisierung und Layout__
    * Linien als einfache oder interpolierte Farbverläufe darstellen (z. B. Gelb → Violett oder Grün → Rot)
    * Transparenz und Farbverläufe helfen, Überlagerungen sichtbar zu machen
    * Maßstab und Titel ergänzen; Hintergrundkarten (ESRI Dark Map) verbessern die Lesbarkeit
5. __Zeitliche Darstellung & Animation__ _(Nach Bedarf zusätzlich zum PNG)_
    * Zeitsteuerung in den Layereigenschaften aktivieren (_Einzelfeld mit Datum/Zeit_)
    * „Objekte über Zeit sammeln“ einstellen, um den Verlauf sichtbar zu machen
    * Zeitlabels als beschrifteten Hilfspunkten hinzufügen (z. B. mit `format_date(@map_start_time, 'd. MMMM yyyy')`)
    * Über das QGIS-Uhrsymbol Animation abspielen und als Einzelbilder exportieren
    * Externe Programme (z. B. Python, Photoshop) nutzen, um GIFs oder Videos aus den Einzelbildern zu erzeugen
## Vorteile der Methode
* __Anschaulichkeit__: Dynamische Animationen machen zeitliche Entwicklungen leicht verständlich, auch für Laien
* __Räumliches Verständnis__: Linien zeigen Start- und Endpunkte, wodurch Flugrichtung, Höhe oder Geschwindigkeit erkennbar werden
* __Flexibilität__: Darstellung möglich als statische Übersichtskarte, animiertes GIF oder thematische Kartenserie
* __Atmosphäre & Visual Storytelling__: Dunkle Hintergründe und Farbverläufe erzeugen eindrucksvolle Darstellungen
* __Open Source__: Umsetzung allein mit QGIS und ggf. kleinen Python-Skripten
## Nachteile der Methode
* __Begrenzte Information__: Ohne Zusatzattribute wie Helligkeit oder Geschwindigkeit bleibt Analyse oberflächlich
* __Unübersichtlichkeit__: Viele Meteore oder überlagerte Linien erschweren die Interpretation
* __Technische Einschränkungen__:
    * QGIS erlaubt keine direkte GIF-Erstellung
    * Zeitformatierung oft nur über externe Tools möglich
* __Qualität__: GIFs haben begrenzte Farben und Bildrate; Hintergrundkarten können bei großem Maßstab unscharf wirken
* __Bedienung & Aufwand__: Viele manuelle Schritte (Datenformatierung, Zeitsteuerung, Export). Layoutpflege und Animationseinstellungen erfordern Fachkenntnisse
* __Fehlende Interaktivität__: Animationen können nicht pausiert oder zurückgespult werden; statische Karten zeigen keine Dynamik

<br><br>
<a id="EP.09"></a>
<br>
# EP.09 | 2.5D-Gebäudemodelle | Chemnitz Innenstadt |
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_09.png)
## Ergebnis
Für einen kleinen Teil des Chemnitzer Stadtgebiets wurde eine Karte erzeugt, welche ein 2,5D Gebäudemodell darstellt. Dabei wurden die Höhen aus den öffentlich zugänglichen Shapefiles LOD2 (Level of Detail 2) erzeugt.
## Arbeitsschritte
1. __Datenbeschaffung__
    * offene 3D Gebäudedaten für den gewünschten Ausschnitt [downloaden](https://www.geodaten.sachsen.de/downloadbereich-digitale-3d-stadtmodelle-4875.html) und in QGIS importieren 
2. __Symbolisierung__
    * in den Layer Eigenschaften den Stil 2.5 D wählen
    * „GebMSHoehe“ als Höhenattribut setzen
    * Blickwinkel auf 90° stellen, Schatten für bessere Performance deaktivieren
3. __Farbgestaltung__ 
    * in der Symbolisierung zu _Einzelsymbol_ wechseln 
    * untere _Einfache Füllung_ eine Gradientenfüllung einstellen und passenden Verlauf auswählen
    * obere Einfache Füllung ebenfalls entsprechend anpassen um ein ansprechendes Design zu erhalten
    * Einstellungsmöglichkeiten: Gradiententyp = konisch, Koordinatenmodus = Ansichtsfenster, Spreizung ggf. auf _Reflektieren_
    * Farben Transparenz oder Zweifarbigkeit können die Illusion verstärken
4. __3D Einstellungen__ 
    * im Reiter 3D Ansicht beim Versatz als Ausdruck „GebMSHoehe“ setzen, um korrekte Gebäudehöhen zu erhalten
    * über _Ansicht_ -> _3D Kartenansicht_ ein separates Fenster öffnen und das Modell interaktiv erkunden
6. __Export__ 
    * Hintergründe (z. B. schwarz) können in den Projekteigenschaften gesetzt werden
    * Ergebnisse lassen sich über _Projekt_ → _Exportieren_ → _als Bild speichern_ oder im Layout als PDF/PNG ausgeben
## Vorteile der Methode
* Anschauliche Darstellung: Gebäudehöhen werden sichtbar, wodurch ein realistischeres, räumliches Verständnis entsteht
* Interaktive Erkundung: Frei dreh- und zoombare 3D-Ansichten erleichtern die Analyse von Stadtstrukturen
* Balance aus Einfachheit und Realität: Modelle sind weniger komplex als vollständige 3D-Geometrien, benötigen weniger Speicher und laufen flüssig bei moderaten Datengrößen
* Vielfältige Nutzung: Eignet sich für Visualisierung von Höhen, Volumen, Abständen, Sichtachsen oder thematischen Analysen (z. B. Emissionen, Besonnung)
* Open Source: Umsetzung ausschließlich mit QGIS ohne zusätzliche Software
* Atlas-Modus: Automatisierte Serienkarten mit konsistentem Layout ermöglichen systematische Visualisierungen und sparen Zeit
## Nachteile der Methode
* Eingeschränkte Genauigkeit: Dachformen und komplexe Gebäudestrukturen werden nicht realistisch abgebildet, Tiefeninformationen fehlen
* Visuelle Grenzen: Unter bestimmten Blickwinkeln können Verzerrungen entstehen
* Performance: Bei sehr großen oder komplexen Datensätzen kann QGIS ins Stocken geraten, Speicher- und Rechenbedarf steigen
* Komplexere Analysen: Für detaillierte 3D-Auswertungen sind spezialisierte Programme oft besser geeignet
* Atlas-Modus: Erfordert Einarbeitung und sorgfältige Pflege von Layout, Beschriftung und Legenden
* Datenaktualität: Modelle können schnell veralten, wenn Geodaten nicht regelmäßig aktualisiert werden
