Dieses Tutorial zeigt Schritt für Schritt, wie Geodaten in R eingelesen und in einer einfachen Darstellung angezeigt werden können.

Die benötigten R-Pakete sind:

- `terra`
- `rgdal`

Die benötigten Shapefiles (3x Vektordaten, 1x Rasterdaten) sind: 

- bezirke_ortsteile.shp
- bahnstationen.shp
- wanderwege.shp
- laermindex.tif

Die Codeschnipsel sollen in R Studio verwendet werden (via Copy and Paste). 


## Vorbereitung

Der erste Teil des Tutorials zeigt, wie man die benötigten R-Pakete installiert und lädt. Es wird auch gezeigt, wie man das Arbeitsverzeichnis einstellt. 

### Installieren der benötigten Pakete

Wenn sie noch nicht installiert sind, werden hier zwei R-Pakete installiert: `terra` und `rgdal`.

```r
pkgs <- c("terra", "rgdal")
install.packages(pkgs[!pkgs %in% installed.packages()])
```

### Laden der benötigten Pakete

Nachdem die Pakete installiert wurden, müssen sie noch geladen werden.


```r
library(terra)
library(rgdal)
```
### Trouble shooting

! Dieser Skriptteil wird nur benötigt wenn die oberen zwei Codeblöcke nicht funktioniert haben, auf privaten Rechnern ist das unwahrscheinlich. 
Wenn beim Laden von `rgdal` keine Fehlermeldung erscheint ist "Das Arbeitsverzeichnis setzen" der nächste Schritt der ausgeführt wird. 

Wenn das laden von `rgdal`auf diesem Weg nicht funktioniert (nach dem Befehl `library(rgdal)` zeigt R einen Fehler an) kann der folgende Code ausgeführt werden:

```r
myPaths <- .libPaths()   # get the paths
myPaths <- c(myPaths[2], myPaths[1])  # switch them
.libPaths(myPaths)  # reassign them
myPaths <- c("C:/Program Files/R/R-4.2.1/library")  
.libPaths()

install.packages("rgdal")
library(rgdal)
```
In diesem Code wird sicher gestellt, dass alle Packete aus dem gleichen Ordner geladen werden. Sind Packete in verschiedenen Ordern installiert funktionieren sie sonst möglicherweise nicht richtig.

(Möglicherweise funktioniert auch dieser Weg noch nicht problemlos, wir arbeiten aber an einer Lösung und updaten den Code sobald sie gefunden ist :) )

### Das Arbeitsverzeichnis setzen

Das Arbeitsverzeichnis wird auf den Pfad der Daten gesetzt. PFAD-ZU-DATEN muss angepasst werden. Wählen Sie hierfür den Pfad zum Ordner mit Ihren Daten, verwenden Sie /. Beachten Sie, dass auch dann eine Liste der Archive innerhalb des ausgewählten Verzeichnisses erstellt wird.

```r
path = "PFAD-ZU-DATEN" # z.B. „C:/Users/doejohn/Documents/GIS/01_Einfuehrung/Daten“
setwd(path)
list.files(path, all.files= TRUE)
```

## Einlesen der Geodaten

### Einlesen der Daten im Arbeitsverzeichnis und Darstellen der Geodaten jeweils in einem einfachen Plot

```r
bezirke <- vect("bezirke_ortsteile.shp")
plot (bezirke)

bahnstationen <- vect("bahnstationen.shp")
plot (bahnstationen)

wanderwege <- vect("wanderwege.shp")
plot (wanderwege)

laermindex <- rast("laermindex.tif")
plot (laermindex)
```

## Gleichzeitiges Darstellen mehrerer Vektordaten

### Darstellen der drei Vektordatensätze innerhalb eines Plots.

Die Darstellungen können „gestapelt“ werden durch Verwendung des Ausdrucks add=TRUE in den nachfolgenden Plot-Befehlen. Informationen zu Farben in R gibt es [hier](https://www.nceas.ucsb.edu/sites/default/files/2020-04/colorPaletteCheatsheet.pdf). 

```r
plot(bezirke, col = "burlywood1") 
plot(bahnstationen, cex =0.5, pch=19, add=TRUE)
plot(wanderwege, lwd= 2, col = "brown", add=TRUE)
```

## Anzeigen von Informationen aus Vektordaten

### Anzeigen der Attributtabelle eines Vektordatensatzes.

Die Attributtabelle kann mit der Funktion `data.frame()` gelesen werden und mit der Funktion `View()` angezeigt werden.

```r
View(data.frame(bezirke))
```

### Anzeigen einer ausgewählten Spalte (z.B. Ortsnamen) der Attributtabelle eines Vektordatensatzes.

Eine Spalte kann angezeigt werden, in dem der Ausdruck `vector-object + $ + column name` verwendet wird.

```r
bezirke$ORTSTNAME
```

## Gleichzeitiges Darstellen verschiedener Geodaten

### Darstellen des Rasterdatensatzes „Lärmindex“ und zusätzliche Visualisierung der drei Vektordatensätze oberhalb des Rasterdatensatzes.

Die Darstellungen können „gestapelt“ werden durch Verwendung des Ausdrucks `add=TRUE` in den nachfolgenden Plot-Befehlen.

```r
plot(laermindex)
plot(bezirke, border="black", add=TRUE) # Keine Fuellung der Polygone, nur Linien sichtbar
plot(bahnstationen, cex =0.5, pch=19, add=TRUE)
plot(wanderwege, lwd= 2, col = "darkred", add=TRUE)
```
