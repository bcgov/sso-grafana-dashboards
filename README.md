# SSO Grafana Dashboards

This repository stores Grafana dashboard definitions as JSON files, automatically backed up via the [Grafana Git Sync](https://grafana.com/docs/grafana/latest/administration/git-sync/) feature. Dashboards are organized by environment and application under `apps/sso/environments/`. The idea here is to be able to use single repository for multiple grafana instances.

## How It Works

Grafana's Git Sync integration is bidirectional — it pulls dashboard JSON files from this repository on a configurable schedule or upon clicking the `Pull` button, and pushes dashboard changes back as pull requests when saving in the Grafana UI. Authentication is handled through a GitHub App installed on the `bcgov` organization and this repository with required permissions.

For more details, please follow this [guide](https://grafana.com/docs/grafana/latest/as-code/observability-as-code/git-sync/git-sync-setup/set-up-before/)

## Prerequisites

- Grafana v13.0 or later (Git Sync is a Grafana Labs feature, enabled by default in Cloud and available in OSS/Enterprise)
- A GitHub App installed on the `bcgov` organization with access to this repository
- The private key (`.pem` file) generated for the GitHub App on `bcgov` account
- Above permissions granted to the GitHub App for this repository

## GitHub App Configuration

### 1. Create or locate the GitHub App

- Create a Github app using your Github account

### 2. Required GitHub App permissions

Ensure the following repository permissions are set on the GitHub App:

| Permission | Access |
|---|---|
| Administration | Read-only    |
| Contents       | Read & Write |
| Metadata       | Read-only    |
| Pull Requests  | Read & Write |
| Webhooks       | Read & Write |

### 3. Install the app on this repository

 Raise a service request at `https://citz-do.atlassian.net/servicedesk/customer/portal/2` to get it installed on `bcgov` org and that `sso-grafana-dashboards` is included in the selected repositories.

### 4. Note the App ID and Installation ID

- **App ID**: found on the GitHub App's general settings page (`https://github.com/settings/apps/<app-name>` or `https://github.com/organizations/bcgov/settings/apps/<app-name>`)
- **Installation ID**: found in the URL when viewing the app installation on the `bcgov` org: `https://github.com/organizations/bcgov/settings/installations/<installation-id>`
- **Private Key**: on the GitHub App's general settings page, scroll to **Private keys** and click **Generate a private key**. A `.pem` file will be downloaded — store it securely.


## Grafana Git Sync Configuration

Login to Grafana instance and navigate to `<GRAFANA_INSTANCE_URL>/admin/provisioning`

1. Click on `Configure` and `Github`
2. Choose `Connect with GitHub App` and `Connect to a new app`
3. Add `Title`, `App Id`, `Installation Id`, and `Private Key`
4. Click on `Configure Repository`
5. Here you have to choose this repository (paste entire url)
6. Choose default branch `main` and path `apps/sso/environments/<FOLDER_NAME>`
7. Update automatic pulling frequency to 3600 sec or 1 hour
8. Check `Enable pull request option when saving`
9. Click on save

## Migration of Existing Dashboards

- Export all the dashboards to your local machine and delete them from Grafana
- Clone this repository to your local machine and checkout the `main` branch
- Create an `apps/sso/environments/<FOLDER_NAME>` folder, copy all the exported dashboard JSON files into it, and push the changes
- Navigate back to grafana and click on `pull` button and it should pull all the dashboards from the repository
- Ensure to set branch protection ruleset on `main` branch to avoid direct commits
- Any changes to dashboard made directly in the instance now requires a pull request

## License

See [LICENSE](LICENSE).
