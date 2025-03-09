# Jenkins : Automate Git Integration

## Prerequisites

- You Should Have the `Git` packet installes on Your Jenkins Server

```bash
sudo apt install git
```

## Plugins

- Go to Plugins Store and make sure that you have installed those plugins :

- --> **Git Integration**
- --> **Github Integration**

- If you are using Gitlab you gonna need to install `Gitlab Integration`

## Configure Git Credentials in Jenkins

On Github :

- Go to your Github Account Settings
- Click on **`Developer settings`**
- Generate a new token
- Save the token in secure file

***

On Jenkins :

- Go to "Manage Jenkins" → "Manage Credentials"
- Click on "Global credentials"
- Click "Add Credentials" on the left menu

- Fill in the form:

- ---> Kind: "Username with password"
- ---> Username: Your Git username
- ---> Password: Yout Github token
- ---> ID: Create a unique ID (e.g., "github-credentials")
- ---> Description: "GitHub access" or similar

## Create a Jenkins Job That Connects to Git

- From Jenkins dashboard, click "New Item"
- Enter a name ("Git-Auto-Build")
- Select "Freestyle project" and click "OK"
- In the configuration page, scroll to "Source Code Management"
- Select "Git"
- Enter your repository URL: `https://github.com/ciscoAnass/Jenkins.git`
- Select the credentials you created earlier
- Specify the branch to build (e.g., `*/main` or `*/master`)

- On Triggers section activate **`GitHub hook trigger for GITScm polling`**
- Add build steps as needed ("Execute shell" to run tests)
- Click "Save"

## Generate API Token for Jenkins User

- Go to "Manage Jenkins" → "Users"
- Select the User you gonna use for the github Integration
- Go to Security → API Token  → **`Add New Token`**
- Save the API Token in secure file

## Set Up Webhook in GitHub

- Go to your GitHub repository
- Click "Settings" → "Webhooks" → "Add webhook"
- For "Payload URL," enter: `http://JENKIN_USER:TOKEN@JENKIN_URL/job/JOB_NAME/build?token=TOKEN` , normally the ouput shoul be look like this : **`http://anass:a7b3d6f2e8c9a0b5f3g7h2j1k9l0p4q@anassaws.ddns.net:8080/job/gitauto/build?token=a7b3d6f2e8c9a0b5f3g7h2j1k9l0p4q`**

- Choose "application/json" for Content type
- For "Which events would you like to trigger this webhook?" select "Just the push event"
- Check "Active"
- Click "Add webhook"

## Test the Integration

- Make a small change to your repository
- Commit and push the change
- Go to your Jenkins dashboard
- Check if your job automatically started
- Click on the build number to view details and console output
