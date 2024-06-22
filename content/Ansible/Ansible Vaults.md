1. Create a `group_vars/` subdirectory named after the group.

2. Inside this subdirectory, create two files named `vars` and `vault`.

3. In the `vars` file, define all of the variables needed, including any sensitive ones.

4. Copy all of the sensitive variables over to the `vault` file and prefix these variables with `vault_`.

5. Adjust the variables in the `vars` file to point to the matching `vault_` variables using jinja2 syntax: `db_password: {{ vault_db_password }}`.

6. Encrypt the `vault` file to protect its contents.
`ansible-vault create --vault-id`
7. Use the variable name from the `vars` file in your playbooks.