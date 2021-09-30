# Postmark Backup

[Postmark](https://postmarkapp.com/) is a superior email processor, and `postmark-cli` is their command line tool to access server info, push and pull templates, and send emails from the terminal.  

We wanted to have a way to backup our Postmark templates automatically, so we coded this Github Actions workflow. It's not fully automatic but it works well enough to backup your templates and server information. 

## Setup

If you *fork* this public repository you get the benefit of being able to pull from upstream if we make changes. However, we think many would want their backups to be private, so we set this repository as a template. You can click "Use this template" to make a detached copy, and then set its visibility as private. You'll need to make any updates manually, though. 

<img width="190" alt="image" src="https://user-images.githubusercontent.com/512328/135370352-c1a1cab8-b935-4c6c-89b7-ab4c9a9be794.png">

There are a couple of steps to get your copy ready for use.  

Adjust the `backup-postmark.yml` workflow yaml file in the `.github/workflows` folder: 

1. Edit the cron schedule entering the time assuming the GMT timezone, to match when you want it to run where you are.
   * `- cron:  "06 11 * * *" # 6th minute of 11th hour daily in GMT` 
2. Edit the github username and email params from eSolia Bot's to a user with permissions to commit to your repo. 
   * `git config --global user.email "esolia.bot@esolia.co.jp"`
   * `git config --global user.name "esolia-bot"`

If you have servers other than "staging" and "production", you can adjust this section:

```
    - name: Pull staging templates
      env:
          POSTMARK_SERVER_TOKEN: ${{ secrets.POSTMARK_SERVER_TOKEN_STG }}
      run: |
          postmark templates pull ./backups/staging --overwrite
    - name: Pull production templates
      env:
          POSTMARK_SERVER_TOKEN: ${{ secrets.POSTMARK_SERVER_TOKEN_PRD }}
      run: |
          postmark templates pull ./backups/production --overwrite 
```

Since you don't have the repository secrets set yet, your workflow will fail. To get it to run, set Github secrets in your repository's settings. I have servers "staging" and "production", so I created three like this: 

```
POSTMARK_ACCOUNT_TOKEN
POSTMARK_SERVER_TOKEN_PRD
POSTMARK_SERVER_TOKEN_STG
```

The `postmark templates pull` command expects a `POSTMARK_SERVER_TOKEN` variable that matches the server in question, so we just assign as appropriate before the respective command in the workflow. Say you have a Postmark server called "dev". You could do it like this: 

```
    - name: Pull dev templates
      env:
          POSTMARK_SERVER_TOKEN: ${{ secrets.POSTMARK_SERVER_TOKEN_DEV }}
      run: |
          postmark templates pull ./backups/dev --overwrite
```

Once you have your secrets in and the workflow file adjusted, your workflow will run on the schedule, or when you push to main. Note also that you can skip running the workflow by prepending `[skip ci]` to your git commit message. This is useful, say, if you want to just update the readme. 
