If you already publish this repo with **GitHub Pages** (for example `.github/workflows/docs.yml`), avoid two hosts deploying the same site: delete that workflow or restrict it to `workflow_dispatch` until you are done moving to Static Web Apps:

```yaml
on:
  workflow_dispatch:
```
