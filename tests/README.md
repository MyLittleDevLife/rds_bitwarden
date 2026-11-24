# Read-only metadata integration test

Run against a development installation of this collection on the controller.
These tests do not connect to managed hosts, rotate passwords or write items.

```sh
ansible-playbook -i localhost, tests/metadata_readonly.yml --check
ansible-playbook -i localhost, tests/metadata_readonly.yml
```

The first command checks valid UUID input without authenticating or invoking
the CLI. Both commands exercise rejection of malformed identifiers before
login. The expected assertion failure is rescued and checked by the test.

To exercise real read-only lookups, supply `metadata_test_item_ids` as a list
of explicitly selected UUIDs and provide the usual authentication variables
through your existing secret mechanism. Include one accessible item, one
nonexistent UUID and a duplicate UUID. Do not put credentials on the CLI.

The integration test checks the four-field output allowlist, deduplication,
automatic session logout and cache removal. It then opens a caller-owned
session, checks that metadata reads preserve it, and closes it in `always`.
Only item identifiers and availability booleans are printed. Treat these
identifiers as internal metadata and restrict access to the test output.

Run the live test without `--check`. Its only filesystem changes are isolated
temporary CLI directories on the controller, which are checked for removal.
The collection's existing login/logout task files are reused unchanged.
