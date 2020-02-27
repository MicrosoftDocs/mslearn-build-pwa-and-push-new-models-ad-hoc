You're now ready to publish your app to Azure. Use the Sandbox's Windows instance to host your web app. Using Azure's Visual Studio Code extension helps streamline the process.

## Create a web app on Azure

## Create a GitHub Action

You now need to create a GitHub Action to build your app whenever you push new code to it and connect the code to the web app you created on Azure. There are two steps to this process.

1. First, create two nested folders in the root of your app called `.github/workflows`. In the `workflow` folder, add a file called `deploy.yml`. This file will be picked up by GitHub actions automatically as a workflow once you commit it to GitHub. Add the following code to `deploy.yml`:

```yaml
on:
    push:
        branches:
            - master

env:
    AZURE_WEBAPP_NAME: 'trickyimages' # set this to your application's name
    AZURE_WEBAPP_PACKAGE_PATH: 'dist' # set this to the path to your web app project, for Vue.js it's dist
    NODE_VERSION: '10.x' # set this to the node version to use

jobs:
    build-and-deploy:
        name: Build and Deploy
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@v2
            - name: Use Node.js ${{ env.NODE_VERSION }}
              uses: actions/setup-node@v1
              with:
                  node-version: ${{ env.NODE_VERSION }}
            - name: npm install, build, and test
              run: |
                  # Build and test the project, then
                  # deploy to Azure Web App.
                  npm install
                  npm run build --if-present
                  npm run test --if-present
            - name: 'Deploy to Azure WebApp'
              uses: azure/webapps-deploy@v1
              with:
                  app-name: ${{ env.AZURE_WEBAPP_NAME }}
                  publish-profile: ${{ secrets.PORTAL_PUBLISH_PROFILE }}
                  package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

Edit this file to set the name of the app to the name you gave your web app in Azure.

2. You are going to create a new secret key in GitHub to create a handshake between Azure and GitHub.

Go to the Azure portal where your web app is hosted. Click on 'Get Publish Profile' to download a file.

![your profile](../media/profile.png)

In your GitHub repo, go to Settings > Secrets. Copy and paste the contents of that file into a new Secret with the name PORTAL_PUBLISH_PROFILE and save it.

Now, when you push new code to your repo, your GitHub action will build and publish the new codebase and your web app will be refreshed.

![Publishing changes](../media/gh-actions.png)

Try it with a new model to watch the changes happen automatically.
