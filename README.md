dropbox-github-action
===


GitHub custom action to upload a file to Dropbox.

- [dropbox-github-action](#dropbox-github-action)
- [Prerequisites](#prerequisites)
  - [Prepare the Dropbox app](#prepare-the-dropbox-app)
  - [Generate the refresh token](#generate-the-refresh-token)
  - [Set the secrets in the repository](#set-the-secrets-in-the-repository)
- [Usage](#usage)



# Prerequisites

First, we need to create the Dropbox app to get the refresh token.

## Prepare the Dropbox app
Create a Dropbox app at [the developer console in Dropbox](https://www.dropbox.com/developers/apps/create). Please make sure to attach the following permissions.

- `files.metadata.write`
- `files.metadata.read`
- `files.content.write`
- `files.content.read`

App key and app secret will be generated. Please save them for later use.

NOTE: The access token generated here in this console will not valid over 4 hours. We need to generate the refresh token to get the access token later.

## Generate the refresh token

Visit the following URI in your browser to get the authorization code.

```
https://www.dropbox.com/oauth2/authorize?client_id=${YOUR_APP_KEY}&response_type=code&token_access_type=offline
```

Then, we can get the refresh token by the following command. Please use the authorization code you have just got in the previous step in the browser. 

```
$ curl https://api.dropbox.com/oauth2/token \
    -d code=${AUTHORIZATION_CODE} \
    -d grant_type=authorization_code \
    -d client_id=${YOUR_APP_KEY} \
    -d client_secret=${YOUR_APP_SECRET}
```

The response will be like the following.

```
{
    "access_token": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "token_type": "bearer",
    "refresh_token": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "account_id": "xxxxxxxxx",
    "uid": "xxxxxxxx"
}
```

Keep the `refresh_token` for later use.

## Set the secrets in the repository

Set the following secrets in the repository settings.

- `DROPBOX_APP_KEY`: The app key of the Dropbox app.
- `DROPBOX_APP_SECRET`: The app secret of the Dropbox app.
- `DROPBOX_REFRESH_TOKEN`: The refresh token of the Dropbox app.


# Usage
Now you are ready to upload the file to Dropbox with the following step in your workflow.

```yaml
- name: Upload to Dropbox
  uses: Lewuathe/dropbox-github-action@v1.0.3
  with:
    dropbox-app-key: ${{ secrets.DROPBOX_APP_KEY }}
    dropbox-app-secret: ${{ secrets.DROPBOX_APP_SECRET }}
    dropbox-refresh-token: ${{ secrets.DROPBOX_REFRESH_TOKEN }}
    source-path: <Path to local file in the github action container space>
    target-path: <Path to the file in the Dropbox. That is supposed to be under the app folder>
    write-mode:  Write mode to upload the file. The default is 'add'. See more detail here. https://www.dropbox.com/developers/documentation/http/documentation#files-upload
```




