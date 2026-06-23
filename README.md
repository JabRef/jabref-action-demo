# JabRef GitHub Action Demo

Demonstration for [JabRef's GitHub action](https://github.com/JabRef/jabref-action).

```yaml
name: Check

on:
  pull_request:

jobs:
  test-action:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Run jabref-action
        uses: jabref/jabref-action@main
        with:
          bibfile: Chocolate.bib
          output-format: github-actions
```

With `output-format: github-actions`, the consistency findings are emitted as
[GitHub Actions workflow commands](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/workflow-commands-for-github-actions#setting-an-error-message),
so they appear as annotations on the pull request and the commit. The raw output looks like:

```
::error file=Chocolate.bib,line=33,col=1,title=Richard_2017:groups::unknown field for entry type Article
::error file=Chocolate.bib,line=42,col=1,title=Parker_2006:groups::unknown field for entry type Article
...
```

The default `output-format` is `errorformat`. Other supported values are `csv`, `github-actions` and `txt`.

## Checking a whole library collection

The [`check-collection.yml`](.github/workflows/check-collection.yml) workflow runs the action against every `.bib` and `.bb` file in the [JabRef/bibtex-library-collection](https://github.com/JabRef/bibtex-library-collection) repository, which is included here as a git submodule.

The submodule tracks the collection's flattened `mirror` branch (shallow clone), so all bibliography files are available without nested submodules:

```ini
[submodule "bibtex-library-collection"]
	path = bibtex-library-collection
	url = https://github.com/JabRef/bibtex-library-collection.git
	branch = mirror
	shallow = true
```

The workflow uses two jobs:

1. **`discover`** — lists every `.bib` and `.bb` file in the submodule and emits them as a JSON array used to build the matrix.
2. **`check`** — consumes the matrix and runs `jabref-action` once per file. It uses `max-parallel: 1` so the files are checked sequentially, and `fail-fast: false` so one file with inconsistencies does not cancel the rest.

> [!NOTE]
> A GitHub Actions matrix is limited to 256 jobs. If the collection ever contains more than 256 `.bib`/`.bb` files, the matrix has to be split (e.g. batched into multiple jobs).
