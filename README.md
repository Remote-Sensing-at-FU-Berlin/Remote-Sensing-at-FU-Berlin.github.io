# Tutorial Website

This repo contains the source code for the tutorials website from the Remote Sensing Lab at FU-Berlin.

If you re-distribute this tutorial series, please acknowledge the original author with the following citation:

> "RSFU Tutorials" (https://github.com/Remote-Sensing-at-FU-Berlin/Remote-Sensing-at-FU-Berlin.github.io) by Remote Sensing Lab at FU-Berlin. The tutorial series is licensed under CC BY SA 4.0.


# How to use

## Updating a document
1. Go to the project repository and change the file
2. Commit your changes

## Creating a new document
1. Go to the project repository and change the file
2. Create a new document
3. Go to [mkdocs.yml](https://github.com/Remote-Sensing-at-FU-Berlin/Remote-Sensing-at-FU-Berlin.github.io/blob/main/mkdocs.yml) and add your document with this syntax (usually, your just need to add the last line)

```yml
nav:
    
    - Project:
        - Subproject:
            - 'Document name': './projectrepository/file_name.md'
```
4. Commit your changes
