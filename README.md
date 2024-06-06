# sfdx-github-workflow-template
This is a sample SFDX repository to demonstrate the Github Workflows and Actions for CI/CD processes using SFDX and CLI

# How To Use SFDX-CLI with GitHub Actions

## Create JWT Auth Flow

### Create Self-Signed Cert and Key

```bash
mkdir -p ~/.ssh/jwt
cd ~/.ssh/jwt
openssl genrsa -des3 -passout pass:SomePassword -out server.pass.key 2048
openssl rsa -passin pass:SomePassword -in server.pass.key -out server.key
rm server.pass.key
openssl req -new -key server.key -out server.csr
```

Enter your company information into the CSR Request prompts

```bash
openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server.crt
```
### Create Salesforce Connected App

1. App Manager -> New Connected App
1. Enter Callback URL: <http://localhost:1717/OauthRedirect>
1. Select Use digital signatures
1. Choose File and upload the server.crt
1. Add the following OAuth scopes:
    1. Manage user data via APIs (api)
    1. Manage user data via Web browsers (web)
    1. Perform requests at any time (refresh_token, offline_access)
1. Click Save
1. Click Manage
1. Click Edit Policies
1. In the OAuth Policies section, select Admin approved users are pre-authorized for permitted users
1. Click Save
1. Click Manage Profiles and then click Manage Permission Sets. Select the profiles and permission sets that are pre-authorized to use this connected app.

### Test

```bash
sf org login jwt --client-id [85 char string] --username username@domain.com --jwt-key-file /home/username/.ssh/jwt/server.key --set-default-dev-hub --alias devjwt
```

### Setup Repository Actions Secrets

Create the following secrets:

* SALESFORCE_CONSUMER_KEY
* SALESFORCE_DEVHUB_USERNAME
* SALESFORCE_JWT_SECRET_KEY
