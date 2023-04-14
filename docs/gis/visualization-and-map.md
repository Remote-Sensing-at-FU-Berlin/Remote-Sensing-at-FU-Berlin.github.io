
Dieses Tutorial zeigt Schritt für Schritt, wie man bestimmte Aufgaben wie
das Einlesen von Shapefiles, das Erstellen von Layout-Karten, das Einfügen 
von Notch-Pfeilen, Skalenbalken, das Ändern von Farben und das Exportieren der 
Ergebnisse ausführt.

Die benötigten R-Pakete sind:
  
- sf
- ggplot2
- ggspatial

Die benötigten Shapefiles sind: 
  
- Luftbelastung_2009.shp

## Vorbereitung
Der erste Teil des Tutorials zeigt, wie man die benötigten R-Pakete
nstalliert und lädt. Es wird auch gezeigt, wie man das Arbeitsverzeichnis
einstellt und Shapefiles einliest.


### Installieren der benötigten Pakete
Wenn sie noch nicht schon installiert sind, werden hier drei `R-Pakete` 
installiert: `sf`, `raster`, und `dplyr`.

```r
if (!requireNamespace("sf", quietly = TRUE)) {
  install.packages("sf")
}
if (!requireNamespace("ggplot2", quietly = TRUE)) {
  install.packages("ggplot2")
}
if (!requireNamespace("ggspatial", quietly = TRUE)) {
  install.packages("ggspatial")
}
```
### Laden der benötigten Pakete
Nachdem die Pakete installiert wurden, müssen sie noch geladen werden.

```r
library(sf)
library(ggplot2)
library(ggspatial)
```

### Das Arbeitsverzeichnis setzen
Das Arbeitsverzeichnis wird auf den Pfad der Daten gesetzt. 
`PATH TO DATA` muss angepasst werden. 
Beachten Sie, dass auch dann eine Liste der Archive innerhalb des ausgewählten 
Verzeichnisses erstellt wird.

```r
path = "PFAD-ZU-DATEN"
setwd(path)
list.files(path, all.files= TRUE)
```

### Einlesen der Shapefiles im Arbeitsverzeichnis
Hier wird unser Shapefile im Arbeitsverzeichnis eingelesen. 
Es wird empfohlen, den gesamten Pfad anzugeben.

```r
shape <- st_read(file.path("PFAD-ZU-DATEN/Luftbelastung_2009.shp"))
```

### Erstellen wir zunächst ein einfaches Diagramm.

```r
plot(shape["PLR_NAME"]) 
```
## Verarbeitung

Wir zeichnen die kontinuierliche Variable "PM25_2009_" 
(Name, der im Fenster "Environment" zu sehen ist).

Titel und Legende ändern, Bildunterschrift wird in das endgültige 
Bild übernommen (Name und Datum ändern)

```r
ggplot() + 
  geom_sf(data = shape, aes(fill = PM25_2009_)) +
  scale_fill_gradient(low = "aliceblue", high = "red", na.value = NA)+ #Für mehr Farben: https://www.datanovia.com/en/blog/awesome-list-of-657-r-color-names/
  labs(title = "PM 2.5 - Berlin 2009", fill= "PM 2.5", caption = "A Trumper, 11.04.2023") + 
  theme_bw() + #für weitere Themen: https://ggplot2.tidyverse.org/reference/ggtheme.html
  coord_sf(expand = TRUE) + xlab("Longitude") + ylab("Latitude") +
  annotation_scale(location = "bl", width_hint = 0.2) +
  annotation_north_arrow(location = "tr", which_north = "true", #Standortoptionen sind: "bl", "tl", "br", "tr" (https://bookdown.org/brianwood1/QDASS/simple-static-maps.html)
                         pad_x = unit(0.1, "in"), pad_y = unit(0.1, "in"), #Zahlen für verschiedene Standorte anpassen
                         style = north_arrow_nautical)  #verschiedene Stile: north_arrow_orienteering, north_arrow_fancy_orienteering, north_arrow_minimal, north_arrow_nautical
```

für weitere Informationen: https://r-spatial.org/r/2018/10/25/ggplot2-sf.html

### Karte exportieren
ggsave() ist eine praktische Funktion zum Speichern eines Plots. 
Standardmäßig wird der zuletzt angezeigte Plot gespeichert, wobei die Größe des aktuellen Grafikgeräts verwendet wird. 
Außerdem wird anhand der Erweiterung der Typ des Grafikgeräts erraten.
Die Datei wird in unserem Ordner "setwd" gespeichert.

```r
ggsave("continuous_map.png", width = 10, height = 10, dpi = 400)
```

dpi = Plot-Auflösung. Akzeptiert auch eine String-Eingabe: "retina" (320), "print" (300), or "screen" (72).

Wir werden nun die diskrete Variable darstellen "PM25_200_1"
(Name, der im Fenster "Environment" zu sehen ist)


Hierfür werden wir ein Layout für eine diskrete Skala erstellen.

```r
cols <- c("gering" = "darkgreen", "mittel" = "darkolivegreen3", "hoch" = "orange", "sehr hoch" = "red")
#Für mehr Farben: https://www.datanovia.com/en/blog/awesome-list-of-657-r-color-names/
```

Titel und Legende ändern, Bildunterschrift wird in das endgültige 
Bild übernommen (Name und Datum ändern)

```r
ggplot() + 
  geom_sf(data = shape, aes(fill = PM25_200_1)) +
  scale_fill_manual(values = cols, breaks=c('gering', 'mittel', 'hoch',"sehr hoch"))+ #Hier verwenden wir die Farben, die wir zuvor definiert haben, und legen die Reihenfolge fest.
  labs(title = "Grad Feinstaubbelastung in Berlin im Jahr 2009", fill= "Grad", caption = "A Trumper, 11.04.2023") + 
  theme_bw() + #für weitere Themen: https://ggplot2.tidyverse.org/reference/ggtheme.html
  coord_sf(expand = TRUE) + xlab("Longitude") + ylab("Latitude") +
  annotation_scale(location = "bl", width_hint = 0.2) +
  annotation_north_arrow(location = "tr", which_north = "true", #Standortoptionen sind: "bl", "tl", "br", "tr" (https://bookdown.org/brianwood1/QDASS/simple-static-maps.html)
                         pad_x = unit(0.1, "in"), pad_y = unit(0.1, "in"), #Zahlen für verschiedene Standorte anpassen
                         style = north_arrow_nautical)  #verschiedene Stile: north_arrow_orienteering, north_arrow_fancy_orienteering, north_arrow_minimal, north_arrow_nautical
```
### Karte exportieren
Mit ggsave() 

```r
ggsave("discrete_map.png", width = 10, height = 10, dpi = 400)
```
