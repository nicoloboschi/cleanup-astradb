# `@nicoloboschi/cleanup-astradb` Github action

This action deletes a [DataStax AstraDB](https://www.datastax.com/products/datastax-astra) database.
To create a database, use the `@nicoloboschi/setup-astradb` [action](https://github.com/nicoloboschi/setup-astradb).

Related actions:
- `@nicoloboschi/setup-astradb` [action](https://github.com/nicoloboschi/setup-astradb): Creates a AstraDB database.
- `@nicoloboschi/cleanup-astradb-env` [action](https://github.com/nicoloboschi/cleanup-astradb-env): Delete databases based on their last usage time.

## Action Inputs

| Input name | Description                                                                               	                     | Required 	 | Default Value |
|------------|-----------------------------------------------------------------------------------------------------------------|------------|---------------|
| token      | Astra DB application token. It needs to have enough permissions to create/delete databases in the organization. | true       |               |
| name       | Name of the database to create.                                                                                 | true       |               |
| wait       | Whether to wait for the database to be deleted or not.                                                          | true       |               |
| env        | Astra DB environment. (ENV, PROD).                                                                              | false      | PROD          |


## Example

```yml
name: Test application that uses AstraDB

on: [ pull_request ]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v3
      - name: Setup AstraDB database
        uses: nicoloboschi/setup-astradb@v1
        id: astradb
        with:
          token: ${{ secrets.ASTRA_DB_TOKEN }}
          region: us-east-2
          cloud: aws
      - run: |
          echo "Database ID is ${{ steps.astradb.outputs.id }}"
          echo "Database API endpoint is ${{ steps.astradb.outputs.api-endpoint }}"
      - name: Cleanup AstraDB database
        uses: nicoloboschi/cleanup-astradb@v1
        if: ${{ always() }}
        with:
          token: ${{ secrets.ASTRA_DB_TOKEN }}
          name: ${{ steps.astra.outputs.name }}
```