# CtrlVector Documentation

Public product documentation, configuration guidance, training, and certification preparation for CtrlVector.

## Local preview

```bash
python -m pip install --requirement requirements-docs.txt
mkdocs serve
```

Open `http://127.0.0.1:8000`.

## Validation

```bash
mkdocs build --strict
```

The documentation source is under `docs/`. Navigation and site settings are governed by `mkdocs.yml`.

## Publishing

Pull requests are validated automatically. Merges to `main` build and deploy the site through GitHub Pages.
