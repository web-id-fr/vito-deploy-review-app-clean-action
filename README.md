# VitoDeploy Review App Clean GitHub Action

Remove a deployed review-application on a [VitoDeploy](https://vitodeploy.com/) instance with GitHub action.

> [!IMPORTANT]  
> This is a GitHub action under development, no release has been made yet. Use with caution!

## Description

This action allows you to automatically delete a review-app site on a server managed by a VitoDeploy instance when you close a pull-request.

It works in combination with this other action which removes create ad setup review-app site when you open a pull-request or push to a branch:
[web-id-fr/vito-deploy-review-app-action](https://github.com/web-id-fr/vito-deploy-review-app-action)

### Action running process

All steps are done using the VitoDeploy API.

- Delete site.
- Delete database.

### Optional inputs variables

The action will determine the name of the site (host) and the database if they are not specified (which is **recommended**).

The `host` is based on the branch name (escaping it with only `a-z0-9-` chars) and the `root_domain`.

For example, a `fix-37` branch with `mydomain.tld` root_domain will result in a `fix-37.mydomain.tld` host.

`database_name` is also based on the branch name (escaping it with only `a-z0-9_` chars).

## Inputs

It is highly recommended that you store all inputs using [GitHub Secrets](https://docs.github.com/en/actions/reference/encrypted-secrets) or variables.

| Input                   | Required | Default | Description                                                                                                                                                                                   |
|-------------------------|----------|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `api_base_url`          | yes      |         | API URL (e.g.: "https://vito.test/api").                                                                                                                                                      |
| `api_token`             | yes      |         | API key (with read & write permissions).                                                                                                                                                      |
| `project_id`            | yes      |         | Project ID.                                                                                                                                                                                   |
| `server_id`             | yes      |         | Server ID.                                                                                                                                                                                    |
| `root_domain`           | no       |         | Root domain under which to create review-app site.                                                                                                                                            |
| `host`                  | no       |         | Site host of the review-app.<br>The branch name the action is running on will be used to generate it if not defined (recommended).                                                            |
| `prefix_with_pr_number` | no       | `true`  | Use the pull-request number as host and database prefix when host is not manually defined.                                                                                                    |
| `fqdn_prefix`           | no       |         | Prefix the whole FQDN (e.g.: "app.")                                                                                                                                                          |
| `pr_number`             | no       |         | Manually define pull-request number (⚠️ Based on `GITHUB_REF_NAME` by default, but does not seems to work properly, according to this [issue](https://github.com/actions/runner/issues/256)). |
| `database_name`         | no       |         | Database name of the review-app site.<br>The branch name the action is running on will be used to generate it if not defined (recommended).                                                   |
| `database_name_prefix`  | no       |         | Database name prefix, useful for PostgreSQL that does not support digits (PR number) for first chars.                                                                                         |

## Outputs

| Output          | Description                                                          |
|-----------------|----------------------------------------------------------------------|
| `host`          | Host of the review-app (generated or forced one in inputs).          |
| `database_name` | Database name of the review-app (generated or forced one in inputs). |

## Examples

Delete a review-app on closed pull-requests:

```yml
name: Delete Review-App

on:
  pull_request:
    types: [ 'closed' ]

concurrency: review-app-${{ github.ref }}

jobs:
  review-app:
    runs-on: ubuntu-latest
    name: "Delete Review-App"

    steps:
      - name: "Delete Review-App on VitoDeploy"
        id: vito-deploy-review-clean-app
        uses: web-id-fr/vito-deploy-review-app-clean-action@main
        with:
          api_base_url: "https://your-vito-instance.tld/api"
          api_token: ${{ secrets.VITO_REVIEW_APP_SERVER_ID }}
          project_id: "1"
          server_id: "1"
          root_domain: "my-review-apps-root-domain.tld"
          pr_number: ${{ github.event.pull_request.number }}

```

## Credits

- [Ryan Gilles](https://github.com/rygilles)

## License

TODO
