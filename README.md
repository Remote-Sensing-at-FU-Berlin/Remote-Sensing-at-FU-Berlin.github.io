# Tutorial Website

This repo contains the source code for the tutorials website from the Remote Sensing Lab at FU-Berlin.

If you re-distribute a tutorial, please acknowledge the original author of the tutorial. More information in the specific repositorys.

# How to use 

## Updating a document
1. Go to the project repository and change the file
2. Commit your changes

## Creating a new document
1. Go to the project repository and create a new document
3. Go to [mkdocs.yml](https://github.com/Remote-Sensing-at-FU-Berlin/Remote-Sensing-at-FU-Berlin.github.io/blob/main/mkdocs.yml) and add your document with this syntax (usually, you just need to add the last line)

```yml
nav:
    
    - Project:
        - Subproject:
            - Document name: ./projectrepository/file_name.md
```
3. Commit your changes

# Markdown
## Headings
The number of hashtags before the headings indicate the level. For example, "Headings" is a level 2 heading, "Unordered List" is a level 3 heading.

## Formatting

*Cursive*

**Bold**

## List

### Unordered list

- Object 1
- Object 2

### Ordered list

1. object 1
2. object 2

## Code block
This is an R code chunk. You can also execute this in R-Studio. Just click the arrow in the upper right corner.

```r
y= 2+2
y
```
Each code chunk will appear later in the HTML as a separate paragraph.

```r
setwd("C:\\Path\\to\\your\\directory")
```

## Quotes
This is what a quote looks like, the second quote also has the font size set smaller. 

> This is a quote in normal font size. 

## Links

This is a [link](https://remote-sensing-at-fu-berlin.github.io/arcgispro-tutorials/bildinterpretation_mit_google_earth/). The part in the square brackets is the display name and the part in the round brackets is the https link.

## Images
Here I include an image that is in the "assets" folder. If I save the images in another folder, you have to specify the relative path accordingly.

![](docs/assets/logo.png)

## Formulas
Formulas can either be inserted as images or you can use the latex formatting:

Equation in a line $A = \pi*r^{2}$   

Equation in a separate line: 

$$E = mc^{2}$$ 

## Other

When you are satisfied, "commit" your changes. This will be updated automatically on the website. Here are some references.

- [Markdown](https://www.markdownguide.org/cheat-sheet/)
- [Advanced Markdown for the website](https://squidfunk.github.io/mkdocs-material/reference/)

- Latex Cheatsheet](https://tilburgsciencehub.com/building-blocks/collaborate-and-share-your-work/write-your-paper/amsmath-latex-cheatsheet/)
- [Official documentation](https://www.latex-project.org/help/documentation/usrguide.pdf)

Have fun!
