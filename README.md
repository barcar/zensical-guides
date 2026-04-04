# Zensical Guides

[![Documentation](https://github.com/barcar/zensical-guides/actions/workflows/docs.yml/badge.svg)](https://github.com/barcar/zensical-guides/actions/workflows/docs.yml) [![Code scanning](https://img.shields.io/badge/code%20scanning-GitHub-1f883d?logo=github)](https://github.com/hypercat-net/zensical-guides/security/code-scanning) [![License](https://img.shields.io/github/license/barcar/zensical-guides)](https://github.com/barcar/zensical-guides/blob/main/LICENSE) [![Docs](https://img.shields.io/badge/docs-zensical--guides.hypercat.net-8b5cf6)](https://zensical-guides.hypercat.net/)

Practical notes on using [Zensical](https://zensical.org/) to build documentation sites. This repository is **not** official Zensical documentation; it collects approaches and deployment patterns that worked in real projects. When something here disagrees with the manual, trust [Zensical’s documentation](https://zensical.org/docs/) first.

**Documentation:** [https://zensical-guides.hypercat.net/](https://zensical-guides.hypercat.net/) — deployment walkthroughs for Azure Static Web Apps (GitHub Actions and Azure Pipelines). The [Zensical “Publish your site”](https://zensical.org/docs/publish-your-site/) page links there from its **Other** section.

[![BuyMeACoffee](https://raw.githubusercontent.com/barcar/buymeacoffee-badges/main/bmc-donate-white.svg)](https://buymeacoffee.com/barcar)

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
