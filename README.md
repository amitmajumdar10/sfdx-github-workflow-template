# sfdx-github-workflow-template
This is a sample SFDX repository to demonstrate the Github Workflows and Actions for CI/CD processes using SFDX and CLI.

For creating the Self-Sign certificate and Connected App for the JWT Auth Flow, follow the below documentation:
 - https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm
 - https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_key_and_cert.htm
 - https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_connected_app.htm

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

* **SALESFORCE_CONSUMER_KEY** - 
  Get the Consumer Key from the Connected App: <img width="660" alt="image" src="https://github.com/amitmajumdar10/sfdx-github-workflow-template/assets/46327185/b281e08b-6a26-45b2-94b5-3cb1011e9a5f">
 
* **SALESFORCE_DEVHUB_USERNAME** -
  The username of the Salesforce user that will be used for the deployment. Please note, the user should have the profile or permission set assigned which is added to the Connected App.
  
* **SALESFORCE_JWT_SECRET_KEY** -
  The content of the secret.key file, generated using OpenSSL.
