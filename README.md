# Postmark Backup

[Postmark](https://postmarkapp.com/) is a superior email processor, and `postmark-cli` is their command line tool to access server info, push and pull templates, and send emails from the terminal.  

We wanted to have a way to backup our Postmark templates automatically, so we coded this github workflow. It's not fully automatic but it works well enough to backup your templates and server information. 

## Setup
### Local

Use `node` and `npm` to install the `postmark` command per the [postmark-cli](https://github.com/wildbit/postmark-cli) repository. You should be able to run `postmark --version` and get a response. 

Fork then clone the repo to your local drive. Let's use `$HOME/dev/postmark-backup` as an example. 

Assuming `postmark` is installed and working, now seed the servers list, replacing the postmark account token `1212121212121212` with your own when you export the variable: 

```
~/dev/postmark-backup ❯ export POSTMARK_ACCOUNT_TOKEN='1212121212121212'
~/dev/postmark-backup ❯ postmark servers list --json > ./backups/postmark-servers-list.json 
```

Seed the templates, pasting the appropriate server token for each server (you could also export `POSTMARK_SERVER_TOKEN` twice). We have servers "staging" and "production", but pull yours into whatever folder names make sense for you: 

```
~/dev/postmark-backup ❯ postmark templates pull ./backups/production --overwrite
# Paste production server token when prompted
~/dev/postmark-backup ❯ postmark templates pull ./backups/staging --overwrite
# Paste staging server token when prompted
```

Now you have some backups you can manually add, commit and push to the repo. 

### On your Fork

Set Github secrets in settings for your repository. I have servers "staging" and "production", so I created three like this: 

```
POSTMARK_ACCOUNT_TOKEN
POSTMARK_SERVER_TOKEN_PRD
POSTMARK_SERVER_TOKEN_STG
```

The `postmark templates pull` command expects a `POSTMARK_SERVER_TOKEN` variable that matches the server in question, so we just assign as appropriate before the respective command in the workflow. 

Adjust the `backup-postmark.yml` workflow in the `.github/workflows` folder: 

1. Edit the cron schedule. 
2. Edit the github username and email params from Rick Cogley's. 
3. Manually enable the workflow. 

Once enabled, you can skip running the workflow by prepending `[skip ci]` to your git commit message. This is useful, say, if you want to just update the readme. 