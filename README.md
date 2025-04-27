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
