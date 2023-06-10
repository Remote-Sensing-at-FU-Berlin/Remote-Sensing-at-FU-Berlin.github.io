In diesem Tutorial lernen wir, wie man mit digitalen Höhenmodellen in R arbeitet. Wir werden Rasterdaten laden, Höhenmodell anzeigen, Hangexposition und Hangneigung berechnen.

Die benötigten R-Pakete sind:

- `raster`

Die benötigten Raster sind:

- DEM.tif

### Installieren der benötigten Pakete

Falls die R-Pakete noch nicht installiert sind, werden diese hier installiert und bestehende Pakete auf den neuesten Stand gebracht. Bitte beachten Sie, dass dieser Vorgang etwas Zeit in Anspruch nehmen kann.

```r
update.packages(ask = FALSE)
pkgs <- c("raster")
install.packages(pkgs[!pkgs %in% installed.packages()])
```

*Die Warnung kann ignoriert werden.*

### Laden der benötigten Pakete

Nachdem die Pakete installiert wurden, müssen sie noch geladen werden.

```r
library(raster)
```

## Arbeitsverzeichnis setzen

Nun weisen wir das Arbeitsverzeichnis zu, das die Rasterdaten enthält:

```r
setwd("PFAD-ZU-DATEN")
```

## Rasterdaten öffnen

Wir öffnen die Datei `DEM.tif` und weisen sie einem Objekt namens DEM zu:

```r
DEM <- raster("DEM.tif")
```

## Digitales Höhenmodell anzeigen

Jetzt zeichnen wir das DEM:

```r
plot(DEM, 
     legend.lab="Höhe (m)r",
     main="Digitales Höhenmodell",
     xlab="Longitude",
     ylab="Latitude",
     col = terrain.colors(255))
contour(DEM, add=TRUE)
```

## Hangexposition berechnen

Wir berechnen die Hangexposition und zeigen sie dann an:

```r
aspect <- terrain(DEM, opt = "aspect", unit = "degrees", neighbours = 8)
brk <- seq(0,360,36)
cols <- topo.colors(length(brk)-1)
plot(aspect, breaks=brk, col=cols, main="Aspect", legend = FALSE)
legend('bottomright', title='Aspect classes',
       legend=c("[0,36[", "[36,72[", "[72,108[", "[108,144[","[144,180[","[180,216[","[216,252[","[252,288[","[288,324[", "[324,360["),
       fill=cols, bg='white')
```

## Hangneigung berechnen

Jetzt leiten wir die Hangneigung ab und zeigen sie an:

```r
slope <- terrain(DEM, opt = "slope", unit = "degrees", neighbours = 8)
brk <- quantile(slope, seq(0,1,0.1))
cols <- terrain.colors(length(brk)-1)
plot(slope, breaks=brk, col=cols, main="Slope", legend = FALSE)
legend('bottomright', title='Steigung Klassen',
       legend=c("1.Q", "2.Q", "3.Q","4.Q","5.Q","6.Q","7.Q","8.Q","9.Q", "10.Q"),
       fill=cols, bg='white')
```

## Hillshade berechnen

Wir berechnen den Hillshade und zeigen ihn an:

```r
hillshade <- hillShade(slope, aspect, angle = 45, direction = 0)
plot(hillshade, main = "Hillshade", legend = FALSE)
```

Mit diesen Schritten können Sie nun mit digitalen Höhenmodellen in R arbeiten!
