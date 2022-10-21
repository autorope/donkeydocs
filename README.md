
<!-- markdownlint-disable MD026 -->
# Donkey Docs&reg;
<!-- markdownlint-restore -->

The source of the documentation gets built in the folder `./site` and is
published to [official site](http://docs.donkeycar.com/).
Our docs use extended markdown as implemented by MkDocs.

## Building the documentation

* create a python3 environment `python -m venv env`
* activate the python environment `source env/bin/activate`
* install MkDocs `pip install mkdocs`
* install MkDocs redirect `pin install mkdocs-redirects`
* `mkdocs serve` starts a local webserver at localhost:8000.  This is a live server; it will be updated when you save any of the .md files in the docs folder.  So you should be running this as you make changes so you can see their effects.
* `mkdocs build` Builds a static site in `./site` directory
* config docs by editing `./mkdocs.yml`

## Linting

Please use markdownlint for checking document formatting.
[markdownlint offical repo](https://github.com/DavidAnson/markdownlint)

You can execute it locally via docker:

```bash
docker run -v $PWD:/markdown:ro 06kellyjac/markdownlint-cli .
```

For linting rules see `.markdownlint`, for now this is very relaxed set.
