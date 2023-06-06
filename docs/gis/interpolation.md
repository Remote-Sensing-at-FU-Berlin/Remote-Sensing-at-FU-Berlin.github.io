Dieses Tutorial zeigt wie die Attributtabelle eines Shapefiles in R aufgerufen wird und wie wir einfache Rechnungen mit der Attributtabelle und der Geometrie eines Shapefiles durchführen können.

In diesem Skript werden

* ein Shapefile anhand von Koordinateninformationen in einer Tabelle erstellt
* das Shapefile mit einer Attributtabelle verbunden
* die räumliche Verteilung von Messwerten mit Hilfe von Thiesenpolygonen in die Fläche gebraucht

## Vorbereitung

### Laden nötiger Packages
Folgende Packages werden benötigt:

* `sf`
* `dplyr`
* `rgdal`
* `ggplot2`
* `deldir`

Wenn es noch nicht schon installiert ist, wird hier noch das benötigte `sf`package, das `rgdal` und das `dplyr`package installiert: 

```r
pkgs <- c("sf", "dplyr", "rgdal", "ggplot2", "deldir")
install.packages(pkgs[!pkgs %in% installed.packages()])

```

Und anschließend geladen:

```r
library(sf)
library(dplyr)
library(rgdal)
library(ggplot2)
library(deldir)
```

### Einlesen der Messstationsliste

Folgende Dateien werden im Working Directory benötigt:

* Berlin_Bezirke.shp

Wenn nötig: Setze das working directory auf den Ordner mit dem Shapefile. (Hier muss der eigene Pfad eingefügt werden!)
Alternativ kann über die Tasten `STRG` + `Shift`(Großschreibtaste) + `H`ein Pop-Up Fenster geöffnet werden, in dem zum entsprechenden Ordner navigiert werden kann.

```r
# setwd("H:/Some_path/Some_folder/06_Daten")
```

Wir laden die Liste der Messstationen in den Arbeitsspeicher.
In R können Sie die Funktion read.table() oder read.csv() verwenden, um die Tabelle einzulesen und in ein Datenobjekt zu laden.

```r
stations <- read.csv2("Niederschlag_1961-1990_Stationsliste.txt", header = TRUE)
```

Und werfen einen kurzen Blick auf die Tabelle:

```r
View(stations)
```

## Spalten umbenennen

Spalten werden umbenannt da in der Orginal-Tabelle die Spaltennamen zwei Punkte enthalten. Das geht nicht!

```r
colnames(stations)[4] <- "geogr_Laenge"
colnames(stations)[3] <- "geogr_Breite"
```

Wenn das Shapefile mit dem Befehl `st_read` Eingelesen wird wird die Attributtabelle direkt mit eingelesen. Mit den Daten kann gearbeitet werden wie mit einem data.frame, das heißt die meisten "normalen" R Befehle können angewendet werden.

Wenn wir nur die Attributtabelle aufrufen wollen können wir die Geometrie Spalten mit `st_drop_geometry` entfernen. So wird die Attributtabelle wie in ArcGIS angezeigt.

```r
st_drop_geometry(bez_shp)
```
## Shapefile aus Tabelle erstellen

In der Tabelle sind die Koordinaten der Stationen angegeben. 
Diese wollen wir räumlich darstellen. 

Hierfür werden die folgenden Schritte benötigt:

### Koordinatensystem definieren 

Das Koordinatensystem, das wir verwenden wollen wird in ein Objekt gespeichert (hier: WGS 1984). So kann es später unkompliziert in Befehle eingefügt werden. 

```r
crs <- st_crs(4326)
```

### Koordinaten erstellen 

Anhand der zugehörigen Tabellenspalten werden die Koordinaten der Punkte der Messstationen in ein räumliches Datenformat überführt. 

```r
coordinates <- st_as_sf(stations , coords = c("geogr_Laenge", "geogr_Breite"), crs = crs)
```

### Shapefile speichern 

Die Koordinaten werden in einem Shapefile gespeichert. Diese Datei kann in jedem GIS geöffnet und dargestellt werden. 

```r
st_write(coordinates, "Stationen.shp")
```
Hinweis: Die Warnung besagt, dass die Feldnamen für den ESRI Shapefile-Treiber abgekürzt wurden. Das ist eine normale Warnung und beeinflusst die Funktionalität des Shapefiles nicht.

## Join

Im folgenden werden gemessene Niederschlagswerte mit den Messstationen verknüpft.

### Laden nötiger Packages
Folgende Packages werden benötigt:

* `readxl`

Wenn es noch nicht schon installiert ist, wird hier noch das benötigte `readxl`package installiert: 

```r
pkgs <- c("readxl")
install.packages(pkgs[!pkgs %in% installed.packages()])
```

Und anschließend geladen:

```r
library(readxl)
```
Die Pfade zur Excel-Datei mit den Niederschlagswerten und dem Shapefile mit den Messstationen werden definieren, um die Daten im folgenden schneller einlesen zu können. 

```r
niederschlag_file <- "Niederschlag_1961-1990.xls"
messstationen_shapefile <- "Stationen.shp"
```

Die Niederschlagsdaten aus der Excel-Datei werden eingelesen und das Einlesen wird mit `View`kontrolliert.

```r
niederschlag_data <- read_excel(niederschlag_file)
View(niederschlag_data)
```
Anhand der Ansicht über `View` sehen wir das noch nicht alles stimmt. 
Die Daten enthalten einen nicht numerischen Wert. Dieser wird mit `NA` überschrieben. Würde der Wert nicht überschrieben käme es in folgenden Schritten zu Fehlermeldungen. 

```r
niederschlag_data <- read_excel(niederschlag_file, na = "NA")
```
Die räumlichen Daten der Messtationen werden eingelesen:

```r
messstationen <- st_read(messstationen_shapefile)
```

Da es unterschiedliche Namen für "Stations_id" in den zwei Tabellen gibt, muss eine Spalte umbenant werden. 
Eine entsprechende Funktion findet sich im Package `dplyr`.

```r
messstationen <- messstationen %>%
  rename(Stations_id = Sttns_d)
```
Da die Spalte "Stations_id" in beiden Datensätzen vorkommt verwenden wir sie für den Join. 

```r
join_spalte <- "Stations_id"
joined_data <- merge(messstationen, niederschlag_data, by = join_spalte, all.x = TRUE)
```

Wir kontrollieren den Join indem wir einen Blick auf die Attributtabelle werfen: 

```r
View(joined_data)
```
Und exportieren anschließend das Shapefile der Stationen mit den angehängten Niederschlagsmesswerten:

```r
export_shapefile <- "Niederschlag_1961-1990_Messstationen_Joined.shp"
st_write(joined_data, export_shapefile)
```
## Räumliche Auswahl der Klimadaten

Anhand des Attributs `Bndslnd`, das für die Bundesländer steht, wählen wir alle Messstationen in Berlin und Brandenburg.
Die Daten werden in ein neues Shapefile gespeichert. 

```r
berlin_brandenburg <- subset(joined_data, Bndslnd %in% c("Berlin", "Brandenburg"))

export_shapefile <- "Niederschlag_1961-1990_Messstationen_BB.shp"
st_write(berlin_brandenburg, export_shapefile)
```
## Projektion des Datensatzes

Wir lesen das Shape für Berlin und Brandenburg neu ein: 

```r
shapefile_BB <- st_read("Niederschlag_1961-1990_Messstationen_BB.shp")
```
Reprojezieren es in das gewünschte Koordinatensystem (WGS 1984 UTM Zone 33N) und speichern es anschließend unter neuem Namen ab:

```r
proj_crs <- "+proj=utm +zone=33 +datum=WGS84 +units=m +no_defs"
stations <- st_transform(shapefile_BB, crs = proj_crs)

st_write(stations, "Niederschlag_1961-1990_Messstationen_BB_WGS1984_UTMzone33N.shp")
```

Für die Darstellung unserer Ergebnisse lesen wir ein weiteres Shapefile ein, das wir im anschluss daran gleich Ploten. 

```r
background_shapefile <- st_read("Berlin_Brandenburg_WGS1984_UTMzone33N.shp")

plot(background_shapefile, col = "white", border = "black", bg = "transparent")
```
## Thiessen-Polygone erstellen

Die Geometrieinformationen der Messstationen werden extrahiert und die X und Y Koordinaten getrennt in neue Objekte abgespeichert. 

```r
coordinates <- st_coordinates(stations$geometry)
x_coords <- coordinates[, 1]
y_coords <- coordinates[, 2]
```

Diese Koordinaten werden zum erstellen der Thiesenpolygone verwendet. 

```r
deldir_obj <- deldir(x_coords, y_coords)

# Extrahieren der Thiessen-Polygone als Polygone
thiessen_polygons <- tile.list(deldir_obj)
View(thiessen_polygons)

# Plot der Thiessen-Polygone
plot(background_shapefile, add= TRUE, col = "white", border = "black", bg = "transparent")
plot(thiessen_polygons, add= TRUE, main = "Thiessen-Polygone")
```

Informationen zum einfärben der Thiesenpolygone finden sich hier: 
https://gis.stackexchange.com/questions/136542/r-function-for-thiessen-polygons
Sie sollen nach dem Jahreswert (in "stations", Spalte "Jahr") eingefärbt werden 
Viel Erfolg!

!!! info

    Bitte beachten Sie, dass dieses Skript noch in Bearbeitung ist und weitere Schritte benötigt, um die Interpolation zu beenden.
