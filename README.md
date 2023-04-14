# Remote-Sensing-at-FU-Berlin.github.io

> **Important Note**: This guide is intended for those who want to make changes to the website's architecture or design. If you only need to perform regular updates or modifications, please refer to the [wiki](https://github.com/Remote-Sensing-at-FU-Berlin/Remote-Sensing-at-FU-Berlin.github.io/wiki) instead.

## Features


- Responsive design for mobile, tablet, and desktop devices


- Customizable theme and styling

- Markdown support for easy documentation writing


- Search functionality


- Automatic table of contents generation

- Following design guidelines from **Freie Universität Berlin**


## Prerequisites


Before you start working on the website, ensure that you have the following prerequisites installed and set up on your system:


1. **[Python](https://www.python.org/downloads/)**: MkDocs is built with Python, so you'll need to have Python installed on your system.


2. **pip**: pip is the package installer for Python. It usually comes bundled with the Python installation.

3. **[Git](https://git-scm.com/downloads)**: To clone the repository and manage version control, you'll need Git installed on your system. You can download Git from the official website: 

4. **[MkDocs](https://www.mkdocs.org/)**: After installing Python and pip, you can install MkDocs by running the following command:

```bash
pip install mkdocs
```

5. **[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)**: is a theme for
 MkDocs that provides a responsive and modern design. Install the theme 
using the following command:

```bash
pip install mkdocs-material
```

## Getting Started
To start working on the website, follow these steps:

1. Clone the repository:

```bash
git clone https://github.com/Remote-Sensing-at-FU-Berlin/Remote-Sensing-at-FU-Berlin.github.io.git
```

2. Navigate to the cloned repository on your local machine:

```bash
cd Remote-Sensing-at-FU-Berlin.github.io
```

3. Start the MkDocs development server by running the following command:

```bash
mkdocs serve
```

4. Open your web browser and go to `http://127.0.0.1:8000` to view the website locally.

As you make changes to the website's source files, the development server will automatically reload the page, allowing you to see your updates in real-time.

5. When you're satisfied with your changes, push your final version of the website using the folling commands. The website will be automatically deployed and updated:

```bash
git add .
git commit -m "Your commit message here"
git push origin main
```

## Optional

Deploy on your own, following this [tutorial](https://pages.github.com/)
