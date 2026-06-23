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
```

Output:

```
Field Presence Consistency Check Result

| entry type | citation key | Eprint | Groups | Number | Pages | Readstatus | URL |
| ---------- | ------------ | ------ | ------ | ------ | ----- | ---------- | --- |
| Article    | Garcia_2018  | -      | -      | o      | -     | -          | -   |
| Article    | Ding_2006    | -      | -      | o      | -     | -          | -   |
| Article    | Richard_2017 | -      | ?      | -      | -     | ?          | -   |
| Article    | Corti_2009   | -      | -      | o      | o     | -          | -   |
| Article    | Cooper_2007  | -      | -      | o      | o     | -          | -   |
| Article    | Tokede_2011  | -      | -      | o      | o     | -          | -   |
| Article    | Keen_2001    | -      | -      | o      | o     | -          | -   |
| Article    | Katz_2011    | -      | -      | o      | o     | ?          | -   |
| Article    | Hooper_2012  | -      | -      | o      | o     | ?          | -   |
| Article    | Tan_2021     | -      | -      | o      | o     | ?          | -   |
| Article    | Fulton_1969  | o      | -      | o      | o     | -          | o   |
| Article    | Parker_2006  | -      | ?      | o      | o     | ?          | -   |
| Article    | Macht_2007   | -      | ?      | o      | o     | ?          | -   |
| Article    | Scholey_2013 | -      | ?      | o      | o     | ?          | -   |
| Article    | Di_Renzo_2012 | -      | ?      | o      | o     | ?          | -   |

x | required field is present
o | optional field is present
? | unknown field is present
- | field is absent
Consistency check completed
```

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
