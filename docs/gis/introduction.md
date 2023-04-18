## Vorbereitung

Installieren der benötigten Packete

```r
## uncomment, if packages are not installed yet
#install.packages ("terra")
#install.packages ("rgdal")
```

# open libraries

```r
library (terra) 
library (rgdal)
```

```r
path = "C:/Users/sieberan/Documents/GIS/01_Einfuehrung/Daten"
setwd(path)
list.files(path, all.files= TRUE) # listet alle verfügbaren Daten auf
```

#Visualize the data

```r
bezirke <- vect("bezirke_ortsteile.shp")
plot (bezirke)
```

```r
bahnstationen <- vect("bahnstationen.shp")
plot (bahnstationen)
```


```r
wanderwege <- vect("wanderwege.shp")
plot(wanderwege, col = "brown")
```

```r
laermindex <- rast("laermindex.tif")
plot (laermindex)
```

# Plot the bahnstationen data and the wanderwege data on top of the bezirke data. 
# Plots can be “stacked” by using the argument add=TRUE in the second plot command.

```r
plot(bezirke, col = "burlywood1") # infos on colors here: https://www.nceas.ucsb.edu/sites/default/files/2020-04/colorPaletteCheatsheet.pdf
plot (bahnstationen, cex =0.5, pch=19, add=TRUE)
plot(wanderwege, lwd= 2, col = "brown", add=TRUE)
```

# Retrieve information from vector data

# Have a look at the tables. 
# The attribute table can be viewed using the function View() and choosing the table using the function data.frame().

```r
View(data.frame(bezirke))
#View(data.frame(bahnstationen))
#View(data.frame(wanderwege))
```

# A column can be accessed by using the expression vector-object + $ + column name

```r
bezirke$ORTSTNAME
```

# Finally, plot the vector data on top of the raster data

```r
plot (laermindex)
plot(bezirke, border="black", add=TRUE) # no fill of polygons, only lines
plot (bahnstationen, cex =0.5, pch=19, add=TRUE)
plot(wanderwege, lwd= 2, col = "darkred", add=TRUE)
```
