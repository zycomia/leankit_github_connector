# leankit_github_connector
Import and monitor github issues & pull requests in leankit

## Getting started
### Installing
##### npm
`
npm install leankit_github_connector
`
##### Git
Download or clone the repo from github

### Configure
In the module root, update config.js with all the relevant account information and connector settings.

###### example config:

```javascript
module.exports.config = {
  accounts: {
    github: {
      username: "octocat",                                    // your github username
      organization: "myCompany",                              // your github organization or username again
      OAuthToken: "c8457065fccdbbca3af4633ce19b213caa6ea39b"  // a github Personal access token. Needs read access to repo, repo_hook, user and notifications
    },
    leankit: {
      account: "myleancompany",  // company (leankit subdomain)
      email: "a@b.com",          // your leankit login email address
      password: 'iAALDp2qdwa8EN' // your leankit account password
    }
  },
  // array of boards on which cards will be created.
  // Can only add a particular boardId once, but can have the same repositories in multiple boards (Will result in duplicate cards)
  boards: [
    {
      boardId: 123456789,                     // board ID of exsisting leankit board (number in url - eg: myCompany.leankit.com/board/123456789 )
      default_drop_lane: "New Github Issues", // --optional-- name of lane new cards will be added to. If not defined, board's default drop lane will be used
      default_close_lane: "Done",             // --optional-- [Must be unique lane name across board] If defined, cards of closed issues and merged pull requests will be moved to this lane
      ignore_pull_requests: true,             // --optional-- [Default = false] If true, no cards will be created for pull requests
      repositories: [                         // a list of repositories to migrate/monitor for this board. Any open issues/pull requests from these repos will result in leankit cards being created
        "Spoon-Knife",
        "Hello-World"
      ]
    }
  ]
};
```

##### Notes - leankit board settings
* You must create an "Issue" card type on boards before running the connector.
* If ignore_pull_requests is not set to false, you must create an "Pull Request" card type on boards before running the connector.
* Card Header option must be set to "Custom Header Text"


### Run
There are three helper scripts available in the lib directory to perform common tasks

##### lib/migrate.js
This script will read all open issues & pull requests for the configured github repos and create cards on the corresponding leankit boards, then exit.

##### lib/monitor.js
This script will monitor github activity and create new cards in leankit or move closed issues and merged pull requests to the configured close lane.
Adheres to github's defined poll interval (typically 60 seconds) to prevent API abuse issues. This means new cards won't show up in leankit instantly.

###### parameters
supports commandline paramter: --start=<ms_since_epoch>
Will only add cards for github events that happen after this time. Defaults to current time.


##### lib/migrate_and_monitor.js
A combination of the above two scripts, that firsts migrates all open issues & pull requests to leankit, then monitors github for further activity.


#### Run in background
Use your favorite daemonizer such as [pm2](https://github.com/Unitech/pm2) or [forever](https://github.com/foreverjs/forever) to run this task in the background.
