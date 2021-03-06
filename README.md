﻿# Issues Slack Bot

This Slack Bot, "Issues Slack Bot" allows users to create Github Issues right from Slack using the slash command. Once the slash command "/github" is entered a dialog appears to enter in the Github issue. Upon submitting the issue from Slack, it will appear in the selected Repo. 

# Creating an "Issue Slack Bot" using Slash Command

## Developer Use-Case

If you are looking for a format to record Github issues straight from a specific Slack channel this simple bot will be able to support with that. You can designate a specific repository and start adding issues right from a Slack channel. 

Current Slack bot only posts issues to a specified Github repository. Slack Bot can be customized to add the following features:

 - Assign issues to a certain developer
 - Add issue to project board
 - Associate issue with a milestone
 - Apply a specific label to issue

## User Work Flow

When a user types in the slash command *"/github"* in Slack, a dialog window pops open. The user can then enter in a *"Title"* and the *"Issue"* they would like to post to the specified repository destination. Once the user hits "Submit" the issue will be posted to the Github repo. 

![IssueSlackBot](https://media.giphy.com/media/WOCUHnMd8Nle9iq2oa/giphy.gif)

# Setup
The following details the setup to get the Issues Slack Bot added to your Slack Channel. The slash command, repo selected and other related features can be updated per your preference. 

This was developed in Node.js, therefore make sure you have [Node.js](https://nodejs.org/en/) installed on your machine. 

Also the current setup involves creating a localhost tunnel with ngrok. Make sure you have [ngrok](https://api.slack.com/tutorials/tunneling-with-ngrok?cvosrc=blog.medium.medium_fy19-q411-making_apps_actionable&cvo_creative=&utm_medium=blog&utm_source=medium&utm_campaign=cd_blog_medium_all_en_developers_ob-null_cr-_ym-) set up on your machine as well. Ngrok will be needed to set your Request URL (more on this later).

## Create a Slack app and Add Slash Command

 1. Sign in to your Slack account and create an application from [here](https://api.slack.com/apps)
 
 2. Enter Slack Name and development workspace then hit **Create App**
 3. Then in **Basic Information** you can find your **Client ID**, **Client Secret**, and **Signing Secret** 
 4. Copy all of these as they will need to be added to your environment file as follows (these all show up as variables in the index.js file so follow the same name in the env file): 
 `CLIENT_ID=649......`
 `CLIENT_SECRET=c4dd.....`
 `SLACK_SIGNING_SECRET=d5ac.....`
5. Further down in **Basic Information** you can add your App Name, Icon, and Short Description under **Display Information**
 6. Now go to **Interactive Components** and turn on *Interactivity* which should show more fields
 7. Enter your *Request URL*, which will be used by Slack to send the data payload when the action is invoked by a user. Since we are using ngrok you would enter: `https://example.ngrok.io/interactive` 
 8. Now go to **Slash Commands** and click *Create New Command* and fill in the following: 
  `Command: /github`
  `Request URL: https://example.ngrok.io/command`
  `Short Description: Create Github Issue`
 9. Now go to **OAuth & Permissions** and click **Install App to Workspace** (app will be installed to the workspace specified in step 1)
 10. Back in **OAuth & Permissions** you can copy the **OAuth Access Token** as it will need to be added to you environment file as follows: `SLACK_ACCESS_TOKEN=xoxp-......`
 11. Also under **OAuth & Permissions,** under *Redirect URLs* press **Add New Redirect URL**, and add the following: `https://example.ngrok.io/oauth`

## Integrating with Github's API to Post Issues to Repo

 1. Log in to your Github account, and go to **Settings**

 2. Under Settings, go to **Developer Settings**
 3. Click **Personal Access Token** and then click **Generate new token**
 4. Give the token a name under **Note**
 5. Under **Select scopes** you can define the access for the personal token (for this example we gave full control of private repositories)
 6. Copy token and add it to the environment file as follows: `GITHUB_SECRET=d9e3....`
 7. The code in `index.js` is currently structured to post issues to this specific repo. To customize you will need to update the following code which follows the structure of : 

`/repos/GITHUB_USER_NAME_OWNER_OF_REPO/REPOSITORY/...`

 `path: '/repos/aelkugia/Issue-Slack-Bot/issues?access_token='+GITHUB_SECRET+'&scope=public_repo'`

 8. Under `var issueBody` we currently set the title, body and assignee for a given issue. This is what will be posted in Github. In this example the assignee is listed as myself. 

(NOTE: Assignee has to be a real Github username. In this JSON, you can also add label/milestone, but similarly they have to exist under the repository's issues section. More details can be found [here](https://developer.github.com/v3/issues/#create-an-issue))
 
`  var issueBody = JSON.stringify({
	  "title": issueData.title,
	  "body": issueData.body,
	  "assignees": ["aelkugia"]
	});`

## Running the app

 1. Clone this repo and run `npm install`
 
 2. Update your environment variables with your API credentials and set to `.env` (see `.env.sample`)
 3. We will be running locally, so ensure you open another terminal, navigate to the directory where you unzipped ngrok and start it by telling it which port we want to expose to the public internet. To do this type the following `./ngrok http 4390` (this port must match what is set in *index.js* where we set `const PORT=4390` )
 4. If ngrok is on your `$PATH` you can simply type the following from any directory `ngrok http 4390`
 5. Now to run the app type `node index.js`


