# How this site is built

This portfolio is documentation treated as code.

- **Source:** every page is a Markdown file in a [public Git repository](https://github.com/ksalavei-by/portfolio).
- **Build:** [MkDocs](https://www.mkdocs.org/) with the Material theme turns the Markdown into a static site.
- **Checks:** a GitHub Actions workflow builds the site on every change and fails if a link is broken.
- **Review:** changes are made on a branch and merged through a pull request.
- **Publishing:** when a pull request is merged into `main`, GitHub Actions deploys the site to GitHub Pages.

To work on it locally:

```bash
pip install -r requirements.txt
mkdocs serve
```
