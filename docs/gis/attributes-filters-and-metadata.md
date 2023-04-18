Zum Abschluss der Sitzung wollen wir einen Teil der heute durchgeführten Operationen mit R wiederholen.

In diesem Skript wird

* ein Shapefile geladen
* die Attributtabelle eines Shapefiles aufgerufen
* räumlich gerechnet (Flächeninhalt)
* in der Attributtabelle gerechnet
* eine Auswahl anhand von Attributen getroffen
* eine Auswahl anhand räumlicher Beziehung getroffen

## Vorbereitung

### Laden nötiger Packages
Folgende Packages werden benötigt:

* `sf`

Wenn es noch nicht schon installiert ist, wird hier noch das benötigte `sf`package installiert: 

```r
pkgs <- c("sf")
install.packages(pkgs[!pkgs %in% installed.packages()])
```

Und anschließend geladen:

```r
library(sf)
```

### Einlesen der Daten

Folgende Dateien werden im Working Directory benötigt:

* Berlin_Bezirke.shp

Wenn nötig: Setze das working directory auf den Ordner mit dem Shapefile. (Hier muss der eigene Pfad eingefügt werden!)

```r
# setwd("H:/Some_path/Some_folder/04_Daten_Hausaufgabe")
```

Wir laden das Shapefile der Bezirke von Berlin in den Arbeitsspeicher:

```r
bez_shp <- st_read("Berlin_Bezirke.shp")
```

Und werfen einen kurzen Blick auf das Shapefile:

```r
plot(bez_shp)
```

## Die Attributtabelle

Wenn wir und die Datei anschauen sehen wir, dass sie die Daten der Attributtabelle sowie geometrische Informationen enthält.

```r
bez_shp
```

Wenn das Shapefile mit dem Befehl `st_read` Eingelesen wird wird die Attributtabelle direkt mit eingelesen. Mit den Daten kann gearbeitet werden wie mit einem data.frame, das heißt die meisten "normalen" R Befehle können angewendet werden.

Wenn wir nur die Attributtabelle aufrufen wollen können wir die Geometrie Spalten mit `st_drop_geometry` entfernen. So wird die Attributtabelle wie in ArcGIS angezeigt.

```r
st_drop_geometry(bez_shp)
```

Da wir die eigentliche Datei bez_shp aber nicht überschrieben haben ist noch alles unverändert (mit Geometrieinformationen) vorhanden.

## Geometrische Berechnungen / Flächenberechnung

Mit dem sf package können viele geometrische Berechnungen durchgeführt werden.

Eine Übersicht der Funktionen hierfür findet sich z.B. auf dem [Cheat sheet des packages](https://github.com/rstudio/cheatsheets/blob/main/sf.pdf).

Als Beispiel wird die Fläche der Bezirke Berlins berechnet:

```r
st_area(bez_shp)
```

In der Console wird angezeigt, in welcher Einheit die Fläche berechnet wurde (Units: [m^2]).

Da wir mit den Flächen weiter arbeiten wollen berechnen wir die Fläche noch einmal und speichern sie in der Attributtabelle in einer neuen Spalte. Wie bei einem data.frame kann über $ und ein neuer Spaltenname eine neue Spalte zu der Attributtabelle hinzugefügt werden.

## Sonstige Berechnungen in der Attributtabelle

Mit der Berechneten Spalte können beliebige andere Rechenoperationen durchgeführt werden. Da die Zahlenwerte recht groß und unhandlich sind können sie z.B. von $m^2$ in $km^2$ umgerechnet werden. Auch diese Berechnung wird in einer neuen Spalte der Tabelle gespeichert.

Da die Zahlen nun nicht mehr in der Einheit Quadratmeter vorliegen werden entfernen wir mit dem Befehl 'as.numeric' die Einheit und rechnen nur mit dimensionslosen Zahlen weiter.

```r
bez_shp$area_km2 <- as.numeric(bez_shp$area_m2) / (1000*1000)
```

Mit den numerischen Spalten können auch alle beliebigen Statistikoperationen durchgeführt werden.

Am einfachten wählen wir die gewünschte Spalte über $ und den Spaltenname an.

```r
min(bez_shp$area_km2)
mean(bez_shp$area_km2)
max(bez_shp$area_km2)

summary(bez_shp$area_km2)
```

## Auswahl nach Attributen

Anhand der Attribute kann eine Unterauswahl des Shapes erstellt werden. Anahnd der Spalte "BezirkName" kann z.B. der Bezirk Tempelhof-Schöneberg ausgewählt werden.

Die Auswahl treffen wir mit einem logischen Ausdruck:

- Wir wählen von der Datei bez_shp -> `bez_shp`
- alle Spalten (Über eckige Klappern mit Komma) -> `[XXX,]`
- Bei denen in der Spalte "BezirkName" -> `bez_shp$BezirkName`
- Steht genau -> `==`
- `"Tempelhof-Schöneberg"`

```r
bez_shp[bez_shp$BezirkName == "Tempelhof-Schöneberg",]
```

Um weiter mit nur diesem Bezirk arbeiten zu können speichern wir ihn unter neuem Namen.

```r
tempsch_shp <- bez_shp[bez_shp$BezirkName == "Tempelhof-Schöneberg",]
```

Ähnlich kann man auch nach numerischen Attributen wählen, z.B. alle Bezirke mit einer Fläche über 60 $km^2$:

```r
bez_shp[bez_shp$area_km2 > 60,]
```

## Auswahl anhand der Geometrie

Mit den Funktionen des sf-packages können auch geometrische Auswahlen getroffen werden. Eine Übersicht der Funktionen hierfür findet sich z.B. auf dem [Cheat sheet des packages](https://github.com/rstudio/cheatsheets/blob/main/sf.pdf).

Beispielhaft werden die Bezirke gewählt, die an Tempelhof-Schöneberg grenzen.

Mit dem folgenden Befehlt wird angezeigt, welche Polygone den Kriterien entsprechen:

```r
st_touches(bez_shp, tempsch_shp, sparse = F)
```

Für das 1., 2., 5., 6. und 10. Polygon im Shape ist die Bedingung erfüllt.

Die Polygone 1, 2, 5, 6 und 10 berühren also das Polygon tempsch_shp.

Über eine kleine Erweiterung des Ausdrucks werden die entsprechenden Polygone gewählt und in einem neuen Objekt gespeichert:

```r
angrenzend_shp <- bez_shp[st_touches(bez_shp, tempsch_shp, sparse = F),]

plot(angrenzend_shp[1])
```
