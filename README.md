# Zensical Guides

Practical notes on using [Zensical](https://zensical.org/) to build documentation sites. This repository is **not** official Zensical documentation; it collects approaches and deployment patterns that worked in real projects. When something here disagrees with the manual, trust [Zensical’s documentation](https://zensical.org/docs/) first.

**Browse the guides:** [https://zensical-guides.hypercat.net/](https://zensical-guides.hypercat.net/)

## What you will find here

| Topic | Description |
| --- | --- |
| [Azure Static Web Apps (GitHub)](https://github.com/barcar/zensical-guides/blob/main/docs/azure-static-web-app-github.md) | Connect a GitHub repo to Azure Static Web Apps and adjust the generated GitHub Actions workflow for `zensical build`. |
| [Azure Static Web Apps (Azure DevOps)](https://github.com/barcar/zensical-guides/blob/main/docs/azure-static-web-app-devops.md) | Same idea for Azure Repos and Azure Pipelines (`azure-pipelines.yml`). |

The [Zensical “Publish your site”](https://zensical.org/docs/publish-your-site/) page links to these Azure guides from its **Other** section.

## Build the site locally

You need Python 3.x and the dependencies in `requirements.txt` (currently [Zensical](https://pypi.org/project/zensical/) only).

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
zensical build --clean
```

The static output is written to `site/` by default. Open `site/index.html` in a browser, or serve the folder with any static file server.

## Project layout

- **`docs/`** — Markdown source for the site (`zensical.toml` sets `docs_dir` here).
- **`zensical.toml`** — Site name, URL, theme, and Markdown extensions.
- **`.github/workflows/`** — CI for publishing (for example GitHub Pages) when you push to the default branch.

## Contributing

Suggestions and corrections are welcome via [issues](https://github.com/barcar/zensical-guides/issues) or pull requests.

## License

This repository is licensed under the [GNU General Public License v3.0](LICENSE).
