In diesem Tutorial werden Sie die Datenstruktur von Vektor Layern kennenlernen: Punkte, Linien und Polygone. Außerdem werden Sie eigene Vektor Layer und Punkt Layer aus einer Tabelle erstellen und lernen wie Sie die einzelnen Elemente auswählen.

Die benötigten R-Pakete sind:
  
- `sf`
- `mapview`

Die benötige Tabelle ist:

- Jungfernheide.csv

## Vorbereitung
### Installieren der benötigten Pakete
Wenn sie noch nicht installiert sind, werden hier zwei `R-Pakete` 
installiert: `sf` und `mapview`.

```r
pkgs <- c("sf", "mapview")
install.packages(pkgs[!pkgs %in% installed.packages()])
```

### Laden der benötigten Pakete
Nachdem die Pakete installiert wurden, müssen sie noch geladen werden.

```r
library(sf)
library(mapview)
```

### Das Arbeitsverzeichnis setzen
Das Arbeitsverzeichnis wird auf den Pfad der Tabelle (Jungfernheide.csv) gesetzt. 
`PATH TO DATA` muss angepasst werden. 
Beachten Sie, dass auch dann eine Liste der Archive innerhalb des ausgewählten Verzeichnisses erstellt wird.

```r
path <- "PFAD-ZU-DATEN"
setwd(path)
list.files(path, all.files= TRUE)
```

## Das `sf`-Paket
Das `sf`-Paket gibt es seit 2016 und ist der neue Standard bei der Arbeit mit Vektor Layern in R. Eine der wichtigsten Neuerungen in `sf` ist eine vollständige Implementierung des Simple Features-Standards. Seit 2003 ist Simple Features weit verbreitet in räumlichen Datenbanken (wie PostGIS) und kommerziellen GIS (z.B. ESRI ArcGIS). Der Simple-Features-Standard definiert mehrere Arten von Geometrien, von denen sieben in der Welt der GIS und der Geodatenanalyse am häufigsten verwendet werden (z.B. point oder multipoint, siehe Kapitel Geometrie (sfg) ).

Das Paket `sf` hat ein hierarchisches Klassensystem mit drei Klassen:

- `sfg`: eine einzelne Geometrie
- `sfc`: mehrere Geometrien (`sfg`) und CRS Informationen
- `sf`: ein Layer bzw. Tabelle (`data.frame`) mit Attributen welche eine `sfc` Geometrie-Spalte beinhaltet

## Erstellen von Vektor Layern
Wie bereits erwähnt, hat das `sf`-Paket ein hierarchisches System von Datenstrukturen, das aus drei Klassen besteht, von einfach bis komplex: `sfg`, `sfc` und `sf`. In diesem Abschnitt werden wir ein Objekt jeder dieser drei Klassen erstellen, um mehr über sie zu erfahren.

### Geometrie (`sfg`)
Objekte der Klasse `sfg`, das heißt, eine einzelne Geometrie, können mit der entsprechenden Funktion für jeden Geometrietyp erstellt werden:

-   `st_point`
-   `st_multipoint`
-   `st_linestring`
-   `st_multilinestring`
-   `st_polygon`
-   `st_multipolygon`
-   `st_geometrycollection`

Sie können nun `sfg` Geometrien erstellen, z.B. eine Punkt Geometrie mit dem Namen `pnt1`, indem Sie die `st_point` Funktion nutzen:

```r
pnt1 <- st_point(c(13.289679, 52.452526))
```
Das Printing eines `sfg`-Objekts in der Konsole ergibt seine WKT-Darstellung:
```r
pnt1
## POINT (13.28968 52.45253)
```
Die Klasse `sfg` beinhaltet drei Bestandteile:
```r
class(pnt1)
## "XY"    "POINT" "sfg"
```
-   `"XY"`: Die Dimension z.B.: `"XY"`, `"XYZ"`, `"XYM"` oder `"XYZM"`. In den meisten Fällen werden Sie erstmal mit zwei-dimensionalen `"XY"`Geometrien arbeiten.
-   `"POINT"`: Der Geometrie-Typ z.B. `"POINT"`, `"MULTIPOINT"`, etc.)
-   `"sfg"`: Die allgemeine Klasse (`sfg` = Simple Feature Geometry)

Hier ist ein weiteres Beispiel um ein `sfg` Objekt zu erstellen. Diesmal erstellen Sie eine `POLYGON` Geometrie mit dem Names `a`, indem Sie die `st_polygon` Funktion nutzen:

```r
a = st_polygon(list(cbind(c(0,0,7.5,7.5,0),c(0,-1,-1,0,0))))
a
## POLYGON ((0 0, 0 -1, 7.5 -1, 7.5 0, 0 0))
class(a)
## "XY"      "POLYGON" "sfg"
```

Wie Sie sehen konnten, erhalten Sie diesmal den Geometrie-Typ `POLYGON`.
Eine einfache Methode der Darstellung bietet die Funktion `plot`:
```r
plot(a, border = "blue", col = "#0000FF33", lwd = 2)
```
Erstellen Sie ein weiteres `POLYGON` mit dem Namen `b`:
```r
b = st_polygon(list(cbind(c(0,1,2,3,4,5,6,7,7,0),c(1,0,0.5,0,0,0.5,-0.5,-0.5,1,1))))
b
## POLYGON ((0 1, 1 0, 2 0.5, 3 0, 4 0, 5 0.5, 6 -0.5, 7 -0.5, 7 1, 0 1))
class(b)
## [1] "XY"      "POLYGON" "sfg"
plot(b, border = "red", col = "#FF000033", lwd = 2)
```
Sie können mit der Funktion `c` mehrere `sfg` Geometrien zu einer Geometrie zusammenfügen, zum Beispiel die beiden `POLYGONE` `a` und `b`:
```r
ab = c(a, b)
ab
## MULTIPOLYGON (((0 0, 0 -1, 7.5 -1, 7.5 0, 0 0)), ((0 1, 1 0, 2 0.5, 3 0, 4 0, 5 0.5, 6 -0.5, 7 -0.5, 7 1, 0 1)))
class(ab)
## [1] "XY"           "MULTIPOLYGON" "sfg"
```
Beachten Sie, dass `c` immer eine einzige Geometrie zurückgibt, die sich aus den Geometrien zusammensetzt. Dies unterscheidet sich von der Sammlung der Geometrien in einer Geometriespalte (`sfc`), in der die Geometrien getrennt gehalten werden, was mit der Funktion `st_sfc` geschieht, zu sehen im nächsten Kapitel.
```r
plot(ab, border = "darkgreen", col = "#00FF0033", lwd = 2)
```
Sie können mit Hilfe verschiedener Funktionen neue Geometrien berechnen. Im folgenden Beispiel wird der Schnittpunkt von `a` und `b` berechnet, wodurch eine neue Geometrie mit dem Namen `i` entsteht. Dazu nutzen Sie die Funktion `st_intersection`, diese und weitere Funktionen zur Berechnung von Geometrien werden Sie im Seminar zur Geoprozessierung weiter vertiefen.
```r
i = st_intersection(a, b)
```
Dadurch haben Sie nun eine `GEOMETRYCOLLECTION` erzeugt:
```r
i
## GEOMETRYCOLLECTION (POLYGON ((7 0, 7 -0.5, 6 -0.5, 5.5 0, 7 0)), LINESTRING (4 0, 3 0), POINT (1 0))
class(i)
## [1] "XY"                 "GEOMETRYCOLLECTION" "sfg"
plot(i, border = "black", col = "darkgrey", lwd = 2)
```
### Geometriespalte (`sfc`)
Erstellen Sie zwei weitere Punkt Geometrien mit den Namen `pnt2` und `pnt3`:
```r
pnt2 = st_point(c(13.393655, 52.517883))
pnt3 = st_point(c(13.351628, 52.545175))
```
Die erzeugten `sfg` Objekte können mit der Funktion `st_sfc` zu einem `sfc` Objekt zusammengefasst werden.
Das `sfc` Objekt beinhaltet zusätzlich zu den Geometrien ein Coordinate Reference System (CRS). Mit dem `crs` Parameter der Funktion `st_sfc` können vier CRS-Typen genutzt werden:
-   Ein EPSG code (z.B. `4326`)
-   Ein PROJ4 string (z.B. `"+proj=longlat +datum=WGS84 +no_defs"`)
-   Ein WKT string
-   Ein `crs` Objekt eines anderen Layers, erzeugt durch `st_crs`

Sie können nun die drei erzeugten Punkt Geometrien, `pnt1`, `pnt2` und `pnt3`, zusammenfügen zu einem `sfc` Objekt mit dem Namen `geom`. Sie geben an, dass es sich bei den Koordinaten um Längen- und Breitengrade (WGS84) handelt und verwenden die einfachste der drei Methoden - einen EPSG-Code (`4326`):
```r
geom = st_sfc(pnt1, pnt2, pnt3, crs = 4326)
```
Die Zusammenfassung sieht so aus:
```r
geom
## Geometry set for 3 features 
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: 52.45253 ymin: 13.28968 xmax: 52.54518 ymax: 13.39366
## Geodetic CRS:  WGS 84
## POINT (13.28968 52.45253)
## POINT (13.39366 52.51788)
## POINT (13.35163 52.54518)
```
Sie können sehen, dass neben den Geometrien selbst und den abgeleiteten Eigenschaften von Geometrie-Typ, Dimension und Begrenzungsrahmen die Geometriespalte die zusätzlichen Informationen über das CRS enthält.
### Layer (`sf`)
Das `sfc` Objekt kann mit einem `data.frame` (die Attributtabelle) kombiniert werden, um ein `sf` Objekt zu erzeugen. Das vorher erzeugte `sfc` Objekt `geom` repräsentiert die Lage dreier Berliner Hochschulen. Sie erstellen nun einen `data.frame` mit nicht-räumlichen Eigenschaften der Hochschulen:
```r
name = c("Freie Universität Berlin", "Humboldt-Universität zu Berlin", "Berliner Hochschule für Technik")
kurzname = c("FU Berlin", "HU Berlin", "BHT")
hochschultyp = c("Universität", "Universität", "Hochschule")
studierendenanzahl = c(33.500,36.566,13.161)
dat = data.frame(name, kurzname, hochschultyp, studierendenanzahl)
dat
##                             name  kurzname hochschultyp studierendenanzahl
## 1        Freie Universität Berlin FU Berlin  Universität             33.500
## 2  Humboldt-Universität zu Berlin HU Berlin  Universität             36.566
## 3 Berliner Hochschule für Technik       BHT   Hochschule             13.161
```
Sie können nun die Attributtabelle `dat` (`data.frame`) mit der Geometriespalte `geom` (`sfc`) zu einem `sf` Objekt mit dem Namen `layer` zusammenfügen. Nutzen Sie dafür die Funktion `st_sf`:
```r
layer = st_sf(dat, geom)
```
Beachten Sie, dass die Reihenfolge der Zeilen von Attributtabelle und Geometriespalte übereinstimmen. Hier sehen Sie das Ergebnis:
```r
layer
## Simple feature collection with 3 features and 4 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: 52.45253 ymin: 13.28968 xmax: 52.54518 ymax: 13.39366
## Geodetic CRS:  WGS 84
##                              name  kurzname hochschultyp studierendenanzahl
## 1        Freie Universität Berlin FU Berlin  Universität             33.500
## 2  Humboldt-Universität zu Berlin HU Berlin  Universität             36.566
## 3 Berliner Hochschule für Technik       BHT   Hochschule             13.161
##                        geom
## 1 POINT (13.28968 52.45253)
## 2 POINT (13.39366 52.51788)
## 3 POINT (13.35163 52.54518)
```
Mit der Funktion `mapview` können Sie den Layer darstellen:
```r
mapviewOptions(fgb = FALSE)
mapview(layer)
```
## Extrahieren von Layer-Eigenschaften
Sie haben,
-   mit einfachen Koordinaten begonnen
-   diese zu einem Geometrie Objekt umgewandelt (`sfg`) mit Funktionen wie `st_point` und `st_polygon`
-   die Geometrien zu einer Geometriespalte (`sfc`) zusammengefügt mit der Funktion `st_sfc` 
-   Attribute hinzugefügt um ein `sf`-Layer zu erzeugen mit der Funktion `st_sf`

Manchmal ist es jedoch notwendig von einem existierenden Layer die einzelnen Komponenten zu erhateln.
-   `sf` → Geometriespalte (`sfc`)
-   `sf` → Attributtabelle (`data.frame`)
-   `sf` → Koordinaten (`matrix`)

Die Geometriespalte (`sfc`) erhalten Sie mit der Funktion `st_geometry`:
```r
st_geometry(layer)
## Geometry set for 3 features 
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: 13.28968 ymin: 52.45253 xmax: 13.39366 ymax: 52.54518
## Geodetic CRS:  WGS 84
## POINT (13.28968 52.45253)
## POINT (13.39366 52.51788)
## POINT (13.35163 52.54518)
```
Die Attributtabelle erhalten Sie mit der Funktion `st_drop_geometry`:
```r
st_drop_geometry(layer)
##                              name  kurzname hochschultyp studierendenanzahl
## 1        Freie Universität Berlin FU Berlin  Universität             33.500
## 2  Humboldt-Universität zu Berlin HU Berlin  Universität             36.566
## 3 Berliner Hochschule für Technik       BHT   Hochschule             13.161
```
Die Koordinaten von `sf`, `sfc` or `sfg` Objekten erhalten Sie mit der Funktion `st_coordinates`. Die Koordinaten sind in einer `matrix`:
```r
st_coordinates(layer)
##          X        Y
## 1 13.28968 52.45253
## 2 13.39366 52.51788
## 3 13.35163 52.54518
```
## Erstellen eines Punkt-Layers aus einer Tabelle
Sie können Punkt-Layer aus Tabellen (`data.frame`) erstellen, mit Hilfe der Funktion `st_as_sf`. Die Tabelle benötigt dafür zwei Spalten mit X und Y Koordinaten. Die Funktion benötigt drei Argumente:
-   `x`— `data.frame` der genutzt werden soll
-   `coords`—Spaltennamen mit den Koordinaten (X, Y)
-   `crs`—Das CRS (`NA` wenn nichts angegeben wird)

Es wird `Jungfernheide.csv` als Beispiel genutzt. Die Tabelle hat zwei Spalten mit den Namen `loc.x.utm32` und `loc.y.utm32`. Die beiden Spalten beinhalten Koordinaten von Baumpositionen in UTM 32N CRS (EPSG code `32632`):
```r
Jungfernheide = read.csv("Jungfernheide.csv")
```
```r
head(Jungfernheide)
##   X ID Baumart Bhd.1   Biomasse loc.x.utm32 loc.y.utm32
## 1 1  1 Robinie  17.2   72.83773    791196.4     5829994
## 2 2  2   Eiche  45.6 1102.23611    791197.4     5829985
## 3 3  3 Robinie  13.7   38.16567    791195.8     5829991
## 4 4  4 Robinie  19.0   96.63759    791193.4     5829989
## 5 5  5 Robinie  11.0   20.45855    791193.2     5829994
## 6 6  6   Eiche  60.0 2169.95400    791189.2     5829994
```
Diese Tabelle kann in einen `sf`-Layer transformiert werden mit der Funktion `st_as_sf`:
```r
Jungfernheide = st_as_sf(Jungfernheide, coords = c("loc.x.utm32", "loc.y.utm32"), crs = 32632)
```
Beachten Sie die richtige Reihenfolge der Koordinaten X und dann Y. `32632` ist der EPSG code der UTM 32N Projektion.
Der `sf`-Layer sieht dann so aus:
```r
Jungfernheide
## Simple feature collection with 10 features and 5 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: 791185.7 ymin: 5829985 xmax: 791197.4 ymax: 5830006
## Projected CRS: WGS 84 / UTM zone 32N
##     X ID Baumart Bhd.1   Biomasse                 geometry
## 1   1  1 Robinie  17.2   72.83773 POINT (791196.4 5829994)
## 2   2  2   Eiche  45.6 1102.23611 POINT (791197.4 5829985)
## 3   3  3 Robinie  13.7   38.16567 POINT (791195.8 5829991)
## 4   4  4 Robinie  19.0   96.63759 POINT (791193.4 5829989)
## 5   5  5 Robinie  11.0   20.45855 POINT (791193.2 5829994)
## 6   6  6   Eiche  60.0 2169.95400 POINT (791189.2 5829994)
## 7   7  7   Eiche  31.6  445.80534 POINT (791189.4 5830000)
## 8   8  8 Robinie  17.0   70.45746 POINT (791188.9 5830002)
## 9   9  9   Buche  15.5  104.71791 POINT (791185.7 5830006)
## 10 10 10   Eiche  13.8   57.68557 POINT (791193.8 5830000)
```
Mit `mapview` können Sie den Layer darstellen. Der Parameter `zcol` kann genutzt werden um ein Attribut mit einer Farbskala darzustellen:
```r
mapview(Jungfernheide, zcol = "Biomasse")
```
## `sf`-Layer Eigenschaften
### Dimensionen
Ein `sf`-Layer ist eine besondere Art von `data.frame`, welcher Geometrien beinhalten. Viele Funktionen die Sie bei einem `data.frame` nutzen können, funktionieren auch bei einem `sf`-Layer.

Zum Beispiel können Sie mit der Funktion `nrow` die Anzahl der Zeilen bzw. der Features erhalten:
```r
nrow(Jungfernheide)
## 10
```
Sie können mit der Funktion `ncol` die Anzahl der Spalten inklusive der Geometrien erhalten:
```r
ncol(Jungfernheide)
## 6
```
Mit der Funktion `dim` erhalten Sie beides:
```r
dim(Jungfernheide)
## 10 6
```
### Räumliche Eigenschaften
Sie können die Funktion `st_bbox` nutzen um die Koordinaten für die räumliche Ausdehnung des `sf`-Layers zu erhalten:
```r
st_bbox(Jungfernheide)
##      xmin      ymin      xmax      ymax 
##  791185.7 5829984.8  791197.4 5830005.7 
```
Mit der Funktion `st_crs` erhalten Sie Informationen zum Coordinate Reference System (CRS):
```r
st_crs(Jungfernheide)
## Coordinate Reference System:
##   User input: EPSG:32632 
##   wkt:
## PROJCRS["WGS 84 / UTM zone 32N",
##     BASEGEOGCRS["WGS 84",
##         DATUM["World Geodetic System 1984",
##             ELLIPSOID["WGS 84",6378137,298.257223563,
##                 LENGTHUNIT["metre",1]]],
##         PRIMEM["Greenwich",0,
##             ANGLEUNIT["degree",0.0174532925199433]],
##         ID["EPSG",4326]],
##     CONVERSION["UTM zone 32N",
##         METHOD["Transverse Mercator",
##             ID["EPSG",9807]],
##         PARAMETER["Latitude of natural origin",0,
##             ANGLEUNIT["degree",0.0174532925199433],
##             ID["EPSG",8801]],
##         PARAMETER["Longitude of natural origin",9,
##             ANGLEUNIT["degree",0.0174532925199433],
##             ID["EPSG",8802]],
##         PARAMETER["False easting",500000,
##             LENGTHUNIT["metre",1],
##             ID["EPSG",8806]],
##         PARAMETER["False northing",0,
##             LENGTHUNIT["metre",1],
##             ID["EPSG",8807]]],
##     CS[Cartesian,2],
##         AXIS["(E)",east,
##             ORDER[1],
##             LENGTHUNIT["metre",1]],
##         AXIS["(N)",north,
##             ORDER[2],
##             LENGTHUNIT["metre",1]],
##     USAGE[
##         SCOPE["Engineering survey, topographic mapping."],
##         AREA["Between 6Â°E and 12Â°E, northern hemisphere between equator and 84Â°N, onshore and offshore. Algeria. Austria. Cameroon. Denmark. Equatorial Guinea. France. Gabon. Germany. Italy. Libya. Liechtenstein. Monaco. Netherlands. Niger. Nigeria. Norway. Sao Tome and Principe. Svalbard. Sweden. Switzerland. Tunisia. Vatican City State."],
##         BBOX[0,6,84,12]],
##     ID["EPSG",32632]]
```
## Weiterführende Informationen
Das hier bereitgestellte Material stammt von den folgenden Seiten. Dort finden Sie auch noch weitere Informationen zu diesem und weiteren Themen:
- https://cran.r-project.org/web/packages/sf/index.html
- https://geobgu.xyz/r/index.html
