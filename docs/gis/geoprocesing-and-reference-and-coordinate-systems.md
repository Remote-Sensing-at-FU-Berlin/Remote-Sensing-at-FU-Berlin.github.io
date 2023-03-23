Dieses Tutorial zeigt Schritt für Schritt, wie man bestimmte Aufgaben wie das Lesen von Shapefiles, das Erstellen von Puffern, das Zusammenführen von geometrischen Objekten, das Schneiden von Objekten und das Exportieren von Ergebnissen durchführt.

Die benötigten Daten und R-Pakete sind:

- R-Pakete: sf, raster, dplyr
- Shapefiles: 
  -  bezirke_ortsteile.shp
  -  protected_areas_Berlin.shp
  -  straßen_berlin_haupt_utm.shp
  -  Wasserschutzgebiete_Berlin.shp
  -  Zufallspunkte_Berlin_1.shp
  -  Zufallspunkte_Berlin_2.shp
- Raster: 
  -  temperatur.tif

## Vorbereitung
Im ersten Teil des Tutorials wird gezeigt, wie man die benötigten R-Pakete installiert und lädt. Es wird auch gezeigt, wie man das Arbeitsverzeichnis einstellt und Shapefiles und ein Raster in R einliest.

### Installieren der benötigten Pakete
Wenn sie noch nicht schon installiert sind, werden hier drei `R-Pakete` installiert: `sf`, `raster`, und `dplyr`.

```r
if (!requireNamespace("sf", quietly = TRUE)) {
  install.packages("sf")
}
if (!requireNamespace("raster", quietly = TRUE)) {
  install.packages("raster")
}
if (!requireNamespace("dplyr", quietly = TRUE)) {
  install.packages("dplyr")
}
```

### Laden der benötigten Pakete

Nachdem die Pakete installiert wurden, müssen sie noch geladen werden.

```r
library(sf)
library(raster)
library(dplyr)
```

### Das Arbeitsverzeichnis setzen

Das Arbeitsverzeichnis wird auf den Pfad der Daten gesetzt. `PFAD-ZU-DATEN` muss angepasst werden.

```r
setwd("PFAD-ZU-DATEN")
```

### Einlesen der Shapefiles im Arbeitsverzeichnis

Hier werden mehrere Shapefiles im Arbeitsverzeichnis eingelesen und als `R-Objekte` gespeichert.

```r
bezirke_ortsteile <- st_read("bezirke_ortsteile.shp")
protected_areas_berlin <- st_read("protected_areas_Berlin.shp")
straßen_berlin_haupt_utm <- st_read("straßen_berlin_haupt_utm.shp")
wasserschutzgebiete_berlin <- st_read("Wasserschutzgebiete_Berlin.shp")
zufallspunkte_berlin_1 <- st_read("Zufallspunkte_Berlin_1.shp")
zufallspunkte_berlin_2 <- st_read("Zufallspunkte_Berlin_2.shp")
```

### Einlesen des Rasters im Arbeitsverzeichnis

Ein Raster im Arbeitsverzeichnis wird eingelesen und als `R-Objekt` gespeichert.

```r
temperatur <- raster("temperatur.tif")
```

## Verarbeitung
Im zweiten Teil des Tutorials werden verschiedene Verarbeitungsschritte durchgeführt. Es werden beispielsweise Puffer erstellt, geometrische Objekte zusammengeführt, Objekte geschnitten und ein Raster maskiert.

### Buffer von straßen_berlin_haupt_utm

Hier wird ein Buffer von der `straßen_berlin_haupt_utm` erstellt und als neues `R-Objekt` buffer gespeichert. Die Bufferbreite beträgt `100` Meter und wird in `dist` angegeben.

```r
buffer <- st_buffer(straßen_berlin_haupt_utm, dist = 100)
```

### Merge von Zufallspunkte_Berlin_1, Zufallspunkte_Berlin_2

Die beiden Objekte `zufallspunkte_berlin_1` und `zufallspunkte_berlin_2` werden zu einem Objekt zusammengeführt und als neues `R-Objekt` merge gespeichert.

```r
merge <- st_union(zufallspunkte_berlin_1, zufallspunkte_berlin_2)
```

### Dissolve von bezirke_ortsteile mit BEZNAME

Die Polygone im Shapefile `bezirke_ortsteile` werden nach `BEZNAME` gruppiert, dann zu einem Polygon vereinigt, als neues `R-Objekt` dissolve gespeichert und in das Polygonformat konvertiert.

```r
dissolve <- bezirke_ortsteile %>%
  group_by(BEZNAME) %>%
  summarize(geometry = st_union(geometry)) %>%
  st_cast("POLYGON")
```

### Union von Wasserschutzgebiete_Berlin und protected_areas_Berlin

Die beiden Shapefiles `wasserschutzgebiete_berlin` und `protected_areas_berlin` werden zu einem Objekt zusammengeführt und als neues ``R-Objekt`` gespeichert.

```r
union <- st_union(wasserschutzgebiete_berlin, protected_areas_berlin)
```

### Clip von protected_areas_berlin des Objektes "Steglitz-Zehlendorf" aus dissolve

Das Polygonobjekt `dissolve` wird nach dem Bezirk `Steglitz-Zehlendorf` gefiltert, und das Shapefile `protected_areas_berlin` wird dann auf das Polygonobjekt zugeschnitten und als neues `R-Objekt` clip gespeichert.

```r
clip <- st_intersection(protected_areas_berlin, dissolve[dissolve$BEZNAME == "Steglitz-Zehlendorf", ])
```

### Clip des temperatur Rasters mit dissolve

Das Raster `temperatur` wird auf das Polygonobjekt `dissolve` zugeschnitten und als neues `R-Objekt` `clip_raster` gespeichert.

```r
clip_raster <- mask(temperatur, dissolve)
```

## Export
Im letzten Teil des Tutorials wird gezeigt, wie man die Ergebnisse in verschiedenen Formaten, wie Shapefiles oder Raster, exportieren kann.

### Export der Shapefiles

Die erstellten Shapefiles werden im Arbeitsverzeichnis als neue Dateien gespeichert.

```r
st_write(buffer, "buffer.shp", overwrite = TRUE, append = TRUE)
st_write(merge, "merge.shp", overwrite = TRUE, append = TRUE)
st_write(dissolve, "dissolve.shp", overwrite = TRUE, append = TRUE)
st_write(union, "union.shp", overwrite = TRUE, append = TRUE)
st_write(clip, "clip.shp", overwrite = TRUE, append = TRUE)
```

### Export des Rasters clip

Das erstellte Raster `clip_raster` wird im Arbeitsverzeichnis als neue Datei gespeichert.

```r
writeRaster(clip_raster, "clip_raster.tif", format="GTiff", overwrite = TRUE)
```

Hier werden die Ergebnisse der Verarbeitungsschritte als Shapefiles und Raster exportiert, die dann in anderen Anwendungen weiterverwendet werden können.
