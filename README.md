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
            - 'Document name': './projectrepository/file_name.md'
```
4. Commit your changes
