# B10-DTM Abschlussaufgabe SoSe25
## Impressum
Autor: Philipp Schädler
Herausgeber: BHT  
Kontakt: phsc3459@bht-berlin.de & philipp.schaedler@gmail.com

<br><br><br>
# Inhaltsverzeichnis
[EP.01 Dasymetrische Choroplethenkarte](#EP.01)<br>
[EP.02 Gitterchoroplethenkarte](#EP.02)<br>
[EP.03 Punktrasterkarte](#EP.03)<br>
[EP.04 Value-by-alpha Mapping](#EP.04)<br>
[EP.05 Tilemaps](#EP.05)<br>
[EP.06 Flowmaps](#EP.06)<br>
[EP.07 Mesh-Daten](#EP.07)<br>
[EP.08 Animationen in QGIS](#EP.08)<br>
[EP.09 3D-Gebäudemodelle](#EP.09)<br>

<br><br>
<a id="EP.01"></a>
<br>
# EP.01 | Dasymetrische Choroplethenkarte (Bevölkerungsdichte Berlins)
![image](https://github.com/phi-schaedler/B10-DTM/blob/b3a8addf33c9140a84596192b511bf04713ea80a/Files/Schaedler_Philipp_Arbeitsaufgabe_01.png)
## Ergebnis
Die fertige A3 Karte umfasst zwei eigenständige Darstellungen der Bevölkerungsdichte in Berlins Planungsräumen:
* Einfache Choroplethenkarte – Hier wird die Bevölkerungszahl jedes Planungsraums auf dessen Gesamtfläche bezogen. Die Dichte wurde in QGIS aus dem Ausdruck EW / ($area / 10 000) berechnet und anschließend mit dem Klassifikationsmodus Natürliche Unterbrechungen (Jenks) in fünf Farbstufen visualisiert.
* Dasymetrische Choroplethenkarte – Zunächst wurde das Corine Land Cover Shapefile gefiltert, sodass nur die urbanen Klassen 111 und 112 (Urban Fabric) übrigblieben. Nach dem Auflösen dieser Polygone zu einem einzigen bewohnten Flächenlayer wurde er mit den Planungsräumen verschnitten. Alle nicht bebauten Teilflächen erscheinen grau und gehen nicht in die Flächen- oder Dichteberechnung ein. Dadurch liegen die Dichtewerte deutlich höher als in der einfachen Karte.
## Arbeitsschritte
1.	Datenbeschaffung – Das Shapefile der Planungsräume Berlins ([LOR 2021](https://www.berlin.de/sen/sbw/stadtdaten/stadtwissen/sozialraumorientierte-planungsgrundlagen/lebensweltlich-orientierte-raeume/)) sowie die Einwohnerzahlen ([CSV, Stand 1/2023](https://www.berlin.de/sen/sbw/stadtdaten/stadtwissen/monitoring-soziale-stadtentwicklung/bericht-2021/tabellen/)) wurden von der Berliner Senatsverwaltung heruntergeladen.
2.	Aufbereitung der CSV – Überflüssige Kopfzeilen und Spalten wurden in Excel entfernt; 1000er Trennpunkte deaktiviert; beim Import nach QGIS erhielten sowohl PLR Nummer als auch Einwohnerzahl den Datentyp Ganzzahl.
3.	Join – Mit „Attribute nach Feldwert verbinden“ wurden die Einwohnerzahlen an die PLR Geometrien angefügt. Damit war der Layer für die einfache Choroplethenkarte vollständig.
4.	Klassifizierung & Layout – In den Layereigenschaften wurde EW / ($area / 10 000) als Ausdruck hinterlegt, Jenks Klassen gewählt und Farben angepasst; Anzahl, Grenzwerte und Legende wurden manuell verfeinert.
5.	Dasymetrische Schritte:
* Filtern der Corine Daten auf Code_18 = 111 OR 112 mittels „Nach Ausdruck extrahieren“.
* Auflösen zu einer einzigen Urban Fabric Fläche.
* Verschneidung dieser Fläche mit dem mit Einwohnerzahlen angereicherten PLR Layer, sodass jeder bebaute Polygonteil seine Einwohnerzahl erbte.
* Neuberechnung von Fläche und Dichte in einer neuen Spalte.
* Symbolisierung: unbewohnte Teile grau, bewohnte Teile als abgestufte Choroplethenkarte; Stil des einfachen Layers wurde kopiert und neu klassifiziert.
## Vorteile der Methode
* Realistischere Kennzahlen – Dichte wird nur auf bebaute Flächen bezogen, unbewohnte Areale verzerren das Ergebnis nicht.
* Klarere räumliche Kontraste – Hot‑Spots hoher oder niedriger Dichte treten deutlicher hervor, was die Interpretation erleichtert.
* Bessere Planungsgrundlage – Präzisere Werte unterstützen Entscheidungen zu Infrastruktur, Freiraum‑ und Wohnraumbedarf.
## Nachteile der Methode
* Datenabhängigkeit – Das Ergebnis ist so zuverlässig wie die Aktualität und Auflösung der Corine Land Cover Daten; Diskrepanzen an den Schnittstellen der Planungsräume können zu Ausreißern führen, die manuell bereinigt werden sollten (hier am Beispiel des Maximalwertes zu sehen, welcher bewusst beibehalten wurde).
* Annahme „keine Bewohner außerhalb Urban Fabric“ – In Großstädten kaum problematisch, in suburbanen oder ländlichen Kontexten aber eine zu grobe Vereinfachung.
* Höherer Aufwand – Filter- , Auflöse- , Verschneidungs-  und Join-Schritte kosten Zeit und Rechenleistung; jede Aktualisierung der Einwohnerzahlen erfordert ein erneutes Durchlaufen des Workflows.

<br><br>
<a id="EP.02"></a>
<br>
# EP.02 | Gitterchoroplethenkarte (Kirschbäume Berlins)
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_02.png)
## Ergebnis
Die Hexagon Choroplethenkarte (Seitenlänge = 500 m) zeigt deutlich, dass Kirschbäume in Berlin nicht gleichmäßig verteilt sind. Vor allem im Osten und Südosten Köpenicks und Teilen des Westens Berlins sind kaum oder keine Kirschbäume aufzufinden. Für eine Analyse dieser Art eignen sich mit Abstand am besten geometrische Auswertemethoden (z.B. wie hier mittels Hexagone).
## Arbeitsschritte
1.	Datenbeschaffung – Bezirksgrenzen und Baumbestand als WFS-Layer aus dem [Geoportal Berlin](https://fbinter.stadt-berlin.de/fb/index.jsp) einfügen
2.	Erstellen des Hexagongitters – Menü Vektor → Recherchewerkzeuge → Gitter erzeugen
* Typ = „Hexagon (Polygone)“, Ausdehnung = Bezirksgrenzen Layer
* Seitenlänge 500 m → kurzer Diagonalabstand ≈ 866,025 m als horizontale & vertikale Schrittweite eingetragen. 
3.	Aggregation (räumlicher Join) – Verarbeitung → Werkzeugkiste → „Attribute nach Position verknüpfen (Zusammenfassung)“
* Ziel Layer = Hexagon Grid, Join Layer = Kirschbaumpunkte
* Operation = Summe der Baumanzahl pro Zelle
4.	Symbolisierung
* Layer Eigenschaften → Symbolisierung → „Abgestuft“
* Spalte = Baumanzahl, Klassifizierung nach Jenks, 6 Farben von Grau (0 Bäume) bis Dunkelpink (462 Bäume)
## Vorteile der Methode
* Unabhängig von Verwaltungsgrenzen – räumliche Muster werden nicht durch Bezirks- oder Ortsteilgrenzen verzerrt
* Gleich große Flächeneinheiten erleichtern statistische Vergleiche (Dichte ↔ Anzahl)
* Lücken sichtbar – graue Zellen heben baumfreie Räume sofort hervor
* Flexibel skalierbar – durch Ändern der Zellgröße lässt sich der Detailgrad anpassen
## Nachteile der Methode
* Wahl der Zellgröße beeinflusst das Ergebnis – zu grob ⇒ Details gehen verloren, zu fein ⇒ Karte wird unruhig
* Künstliche Zellgrenzen können reale räumliche Strukturen zerschneiden oder Hot Spots „aufspalten“
* Weniger vertraut für Laien als bekannte Bezirkskarten; erfordert Erläuterung im Bericht

<br><br>
<a id="EP.03"></a>
<br>
# EP.03 | Punktrasterkarten (AirBnB Berlin)
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_03.png)
## Ergebnis
Die Punkt-Rasterkarte zeigt, dass sich AirBnB-Angebote in Berlin stark auf die innerstädtischen Bezirke konzentrieren. In den 2x2 km-Zellen von Mitte, Kreuzberg, Friedrichshain und Prenzlauer Berg treten sowohl die höchsten Angebotszahlen (bis > 350 Wohnungen/Häuser pro Raster) als auch die höchsten Durchschnittspreise (> 200 EUR/Nacht) auf. In den Außenbezirken nimmt die Zahl der Inserate deutlich ab, und die Preise liegen überwiegend unter 100 EUR/Nacht. Hotelzimmer häufen sich erwartungsgemäß im touristischen Zentrum innerhalb der S-Bahn-Ringbahn, während geteilte und private Zimmer häufiger in angrenzenden Wohngebieten erscheinen. Insgesamt erlaubt das mehrdimensionale gleichmäßige Raster, Hot-Spots schnell zu erkennen.
## Arbeitsschritte
1.	Datenbeschaffung – [InsideAirBnB-Portal](https://insideairbnb.com/) -> listings.csv (Quelldaten aufbereiten -> auf Extremwerte achten, NULL-Werte entfernen)
2.	Datenimport in QGIS – CSV als Punktgeometrie importieren
3.	Attributaufbereitung und Join – Preis (price) und Objekt-ID zusammenfassen; Anzahl und Durchschnitt pro Typ berechnen.
4.	Rastererstellung – 2x2 km Grid über das Stadtgebiet generieren; Spatial Join -> Zuteilung jeder Anzeige zur entsprechenden Rasterzelle
5.	Kategorisierung – Vier Objektarten: Wohnungen/Häuser, Geteilte Zimmer, Private Zimmer, Hotelzimmer
6.	Symbolisierung – zentrierte Füllung für jede Objektart
* Punktgrösse = Durchschnittspreis pro Nacht
* Farbe = Anzahl der Objekte je Kategorie
* Offsetrichtung = Objektart
## Vorteile der Methode
* Vergleichbarkeit – Jede Rasterzelle besitzt identische Fläche - statistische Kennzahlen (Dichte, Mittelwerte) sind direkt vergleichbar und nicht von Verwaltungsgränzen abhängig
* Hoher Informationsgehalt – Bei guter Umsetzung kann eine sehr hohe Informationsdichte vermittelt werden ohne, dass die Karte überladen wirkt.
* Visualisierung – Gleichmässiges Raster erzeugt ein regelmässig strukturiertes Kartenbild, das Hot-Spots sofort erkennbar macht.
## Nachteile der Methode
* Verlust lokaler Details – Innerhalb einer 2 km-Zelle können starke Unterschiede verborgen bleiben (z. B. Kiezzentren vs. Nebenstrassen); für eine höhere räumliche Auflösung muss das Kartenformat geändert werden, damit Details noch erkennbar bleiben.
* Interpretationshürde – Nicht-fachliche Leserinnen und Leser sind an Bezirksgrenzen gewöhnt; Rasterzellen sind weniger intuitiv verortbar.
* Parameter-Abhängigkeit – Ergebnisse hängen stark von Rastergrösse und -ausrichtung ab; andere Gittergrössen können andere Muster suggerieren.
* Symbolüberladung – Mehrere Variablen (Grösse + Farbe + Kategorie) in dicht bebauten Gebieten führen leicht zu visuellem Clutter und erschweren das Kartenlesen.

<br><br>
<a id="EP.04"></a>
<br>
# EP.04 | Alpha-by-Value (Bundestagswahl 2021/25)
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_04.png)
## Ergebnis
Die Auswertung liefert je Wahljahr eine thematische Karte, in der jeder Wahlkreis in der Parteifarbe der siegreichen Zweitstimme eingefärbt ist. Die Deckkraft der Fläche steigt proportional zum Stimmenanteil der Gewinnerpartei. So werden auf einen Blick sowohl der Wahlsieger als auch die Stärke seines Vorsprungs sichtbar, was den Vergleich zwischen 2021 und 2025 erleichtert. 
Anmerkung: Beim Export wurden die Farben verzerrt. Besonders deutlich wird dies beim Rot der SPD.
<br><br>
Eine Weitere Karte wurde für den Zuwachs der Afd zwischen 2021 und 2025 erstellt: 
[Zuwachs der AfD](https://github.com/To-David/B10-DTM/blob/07fc2314d888f1e9093d5af6cf99fb3469437fb2/files/25-04-24_David_%C3%9C4_Bundestagswahl_Zweitstimmen_AfD.png)
## Arbeitsschritte
1. Datenbeschaffung – Wahlkreis Shapefile sowie finale Zweitstimmenergebnisse 2025 und 2021 von der Bundeswahlleiterin [herunterladen](https://www.bundeswahlleiterin.de/bundestagswahlen/2025/wahlkreiseinteilung/downloads.html). 
2. Datenaufbereitung – In Excel alle irrelevanten Spalten/Zeilen löschen, nur Zweitstimmen der Parteien SPD, CDU/CSU, Grüne, Linke und AfD behalten, CSU Werte in Bayern zur CDU addieren, anschließend als CSV speichern. 
3. Import – Shapefile und CSV in QGIS laden; Wahlkreisnummer als Integer definieren und korrektes Trennzeichen wählen.
4. Verknüpfung – CSV mit Geometrien per Wahlkreisnummer („WKR_NR“) joinen.
5. Attributberechnung – Neue Felder erzeugen
* g_value (Stimmenzahl der Gewinnerpartei) array_max( array( "SPD" , "CDU" , "Gruenen" , "AFD" , "Linke"))
* g_name (Name der Gewinnerpartei - ) with_variable( 'maxVal', array_max( array( "SPD" , "CDU" , "Gruenen" , "AFD" , "Linke")), CASE WHEN "SPD" = @maxVal THEN 'SPD' WHEN "CDU" = @maxVal THEN 'CDU' WHEN "Gruenen" = @maxVal THEN 'Gruenen' WHEN "AFD" = @maxVal THEN 'AFD' WHEN "Linke" = @maxVal THEN 'Linke' END)
* g_proz_gueltig (Anteil an gültigen Stimmen) "G_value" / "gueltig" *100
6. Symbolisierung – Regelbasierte Darstellung pro Partei (z.B. Regel: „g_name“ = ‚SPD‘ und Beschriftung: ‚SPD‘)
* anschließend eine Regel für Alpha anlegen
* Intensität des Kanals wird über den folgenden Ausdruck geregelt: set_color_part( 'black','alpha',scale_linear( "g_proz_gueltig", 19,47,255,0))
## Vorteile der Methode
* Informationsdichte – Parteifarben zeigen den Sieger, die Transparenz dessen Stimmenanteil; zwei Kennzahlen in einer Kartenebene.
* Intuitive Lesbarkeit – Bekanntes Farbschema der Parteien macht die Karte ohne umfangreiche Legende verständlich.
* Homogene Datenquelle – Alle Ausgangsdaten stammen von derselben Behörde, wodurch Kompatibilitätsprobleme minimiert werden.
## Nachteile der Methode
* Farbüberschneidungen – Ähnliche Farbtöne (z. B. helles Rot vs. Rosa) können die Siegerpartei verschleiern und reduzieren die Anzahl unterscheidbarer Kategorien. Dies wird nochmals Anspruchsvoller durch die Nutzung des Alpha-Wertes.
* Manueller Aufwand – Das händische Säubern und Umstrukturieren der Wahldaten ist zeitintensiv und fehleranfällig.
* Lesbarkeit bei vielen Parteien – Je mehr Kategorien eingefärbt werden müssen, desto eher leidet die Klarheit der Darstellung.
* Alpha Skalierung – Bei knappen Ergebnissen kann der Transparenzeffekt zu schwach sein, bei Erdrutschsiegen zu dominant. 

<br><br>
<a id="EP.05"></a>
<br>
# EP.05 | Tile-Mapping (Anteil der zulassungsbeschränkten Studiengänge je Bundesland)
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_05.png)
## Ergebnis
Die erstellte Tile Map Karte macht auf einen Blick sichtbar, wie stark der Anteil zulassungsbeschränkter Studiengänge zwischen den Bundesländern variiert. Stadtstaaten und hochschulstarke westdeutsche Länder weisen die höchsten Quoten auf, während mehrere Flächenländer im Osten deutlich unter dem Bundesdurchschnitt liegen. 
## Arbeitsschritte
1. Datenvorbereitung – CSV Tabelle
* Liste der Länderkürzel und Namen anlegen
* Spalte mit Prozentwerten der [zulassungsbeschränkten Studiengänge](https://hochschuldaten.che.de/) ergänzen
* Datei als CSV speichern
2. Grundlage – Gitter erzeugen
* Rechteckiges Raster über Deutschland skizzieren
* Sechzehn Zellen auswählen, die der Lage der Bundesländer möglichst gut entsprechen
3. Geometrie anpassen – Verschieben & Skalieren
* Kacheln gemäß Aufgabenstellung in einem 4x4 Raster anordnen, sodass die relative geographische Lage weitestgehend erhalten bleibt.
4. Attributpflege – Kürzel zuweisen
* Neue Spalte im Kachel Layer erstellen
* Jede Zelle mit passendem Länderkürzel versehen
5. Tabellenverknüpfung – Join nach Feldwert
* CSV per Länderkürzel mit dem Kachel Layer verbinden
6. Symbolisierung – Abgestufte Darstellung
* Farbverlauf auf die Prozentspalte anwenden
## Vorteile der Methode
* Tile Mapping – klare Lesbarkeit: Gleich große Felder eliminieren Flächenverzerrung und erleichtern den Vergleich
* Bearbeitung in QGIS – geringe Softwarehürde: Alle Schritte lassen sich mit Bordmitteln ohne Zusatzplugins ausführen
## Nachteile der Methode
* Tile Mapping – begrenzte Genauigkeit: Stadtstaaten wie Berlin oder Bremen lassen sich nur annähernd platzieren
* Einheitsgröße – Informationsverlust: Flächenbezogene Aspekte (z. B. Hochschuldichte) gehen verloren

<br><br>
<a id="EP.06"></a>
<br>
# EP.06 | Flowmaps (Syrische Flüchtlinge 2010-2024)
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_06.png)
## Ergebnis
Zwischen 2010 und 2024 verließen rund 7 Mio. Flüchtlinge Syrien. Die Fluchtströme lassen sich in fünf Größenklassen gliedern (10.000 – 3,2 Mio). Der räumliche Schwerpunkt blieb klar auf Syriens Nachbarländern; europäische Ziele traten v. a. ab 2014 / 2015 hinzu und verstetigten sich bis 2024. Auf dieser Karte wurde der Übersicht halber ein Cutoff von 10.000 gewählt.
<br>Anmerkung: Qualität leidet deutlich unter der Aufgabenstellung "PNG mit 1080x1080px"
## Arbeitsschritte
1. Datenbeschaffung – [UNHCR Flüchtlingszahlen](https://www.unhcr.org/refugee-statistics/download) und weltweite [Landesgeometrien](https://www.naturalearthdata.com/downloads/) herunterladen
2. Tabellen Aufbereitung – Pivot Tabelle (Spalten = Jahr, Zeilen = Asylstaat ISO, Werte = Summe Flüchtlinge) erstellen, bereinigte Tabelle als CSV exportieren
3. Layer Import – Länderflächen sowie CSV in QGIS laden; CSV ohne Geometrie als Text Layer einbinden
4. Zentroid Berechnung – Mittelpunkte aller Staaten erzeugen und gegebenenfalls bei Überseegebieten korrigieren
5. Projektion – Eigene orthographische Projektion (+proj=ortho +lat_0=50.3 +lon_0=30.3 +x_0=0 +y_0=0 +a=6371000 +b=6371000 +units=m +no_defs) anlegen, um eine Globusansicht abzubilden
6. Geometrie Join – CSV über ISO Code mit Zentroid Layer verknüpfen
7. Linien Generierung – Im Feldrechner feste Ursprungkoordinaten (Syrien) und Zielkoordinaten ($x, $y) anlegen; „XY → Linie“ Werkzeug ausführen
8. Symbolisierung
* Linienbreite nach Flüchtlingszahl staffeln (5 Klassen)
* Syrien als roten, großzügig skalierten Punkt kennzeichnen
* Ozeanflächen dunkelblau / shapeburst füllen, um Globuseffekt zu erzielen
## Vorteile der Methode
* Räumliche Kontextualisierung – Herkunft und Ziel stehen in direktem Bezug, Richtung & Distanz sind sofort erkennbar
* Vergleichbarkeit – Graduierte Linienbreiten ermöglichen einen schnellen quantitativen Vergleich zwischen Aufnahmestaaten
* Visuelle Attraktivität – Orthographische Projektion liefert eine eingängige, fast globusähnliche Darstellung, die auch Laien anspricht
## Nachteile der Methode
* Linienüberlagerung – Viele ähnliche Pfade können das Bild überladen und Details verdecken, Libanon mit dem zweitgrößten Anteil wird komplett verdeckt
* Zentroid Ungenauigkeit – Staaten mit Überseegebieten erhalten falsche Mittelpunkte und müssen händisch korrigiert werden
* Begrenzter Ausschnitt – Die orthographische Projektion zeigt nur die halbe Erde; Randgeometrien werden teilweise nicht korrekt dargestellt (z.B. gerade Linien bei Brasilien, Kanada) 

<br><br>
<a id="EP.07"></a>
<br>
# EP.07 | Mesh-Daten (Orkan Daria)
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_07.gif)
## Ergebnis
Ein GIF zeigt zeigt wie Orkan Daria Ende Januar 1990 mit über Westeuropa und Deutschland hinwegzieht. Es veranschaulicht die Bewegung des Windes über eine zeitliche Abfolge im Stundentakt. Im Frame sind Datum und Uhrzeit und Windvektoren aus einem GRIB Mesh Datensatz enthalten. 
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
  * Extern ([Photoshop](https://www.adobe.com/africa/learn/photoshop/web/make-animated-gif)) oder Python-Skript) Bilder zu einem GIF zusammenfügen
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
# EP.08 | Animationen in QGIS 
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
# EP.09 | 2.5D-Gebäudemodelle (Chemnitz)
![image](https://github.com/phi-schaedler/B10-DTM/blob/61ab35092725aa2dbd949466cb1f6756d207d618/Files/Schaedler_Philipp_Arbeitsaufgabe_09.png)
## Ergebnis
Für einen kleinen Teil des Chemnitzer Stadtgebiets wurde eine Karte erzeugt, welche ein 2,5D Gebäudemodell darstellt. Dabei wurden die Höhen aus den öffentlich zugänglichen Shapefiles LOD2 (Level of Detail 2) erzeugt.
## Arbeitsschritte
1. __Datenbeschaffung__
    * offene 3D Gebäudedaten für den gewünschten Ausschnitt [herunterladen](https://www.geodaten.sachsen.de/downloadbereich-digitale-3d-stadtmodelle-4875.html) und in QGIS importieren 
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
