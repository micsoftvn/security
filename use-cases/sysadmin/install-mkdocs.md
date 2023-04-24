# Install Mkdocs

**1. Install mkdocs**

Make file `requirements.txt`

```
Babel==2.8.0
click==7.1.1
future==0.18.2
gitdb==4.0.4
GitPython==3.1.1
htmlmin==0.1.12
Jinja2==2.11.2
joblib==0.14.1
jsmin==2.2.2
livereload==2.6.1
lunr==0.5.6
Markdown==3.2.1
MarkupSafe==1.1.1
mkdocs==1.1
mkdocs-awesome-pages-plugin==2.2.1
mkdocs-git-revision-date-localized-plugin==0.5.0
mkdocs-material==5.1.1
mkdocs-material-extensions==1.0b1
mkdocs-minify-plugin==0.3.0
nltk==3.5
Pygments==2.6.1
pymdown-extensions==7.0
pytz==2019.3
PyYAML==5.3.1
regex==2020.4.4
six==1.14.0
smmap==3.0.2
tornado==6.0.4
tqdm==4.45.0
```

```
pip install -r requirements.txt
```

**2. Make a project and build it**

```
mkdocs new auto_doc
cd auto_doc
mkdocs build
```

REF : [https://www.mkdocs.org/](https://www.mkdocs.org/)
