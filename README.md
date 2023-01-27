## Setup
Go to your repo actions settings: https://github.com/company/repo/settings/actions \
Scroll to "Workflow permissions" \
Select Read and Write permissions \
Save

Check action.yml for usage

Example:
```
name: z.backup

on:
  workflow_dispatch:
  schedule:
    # Monday,Wednesday,Friday at 3am (Atlassian only allows backups once every 48h)
    - cron:  '0 3 * * 1,3,5'

jobs:
  backup:
    runs-on: ubuntu-20.04
    steps:
    - uses: actions/checkout@v3

    - name: Run backup
      uses: xucian/actions.atlassian.backup@v1
      with:
        host: yoursite.atlassian.net
        account: youratlassianaccount@examplemail.com
        # Key retrieved from Atlassian (Recommended to have a specific account with read-only permissions - paid plan needed)
        key: ${{ secrets.ATLASSIAN_BACKUP_API_KEY }}

    # Optional
    - name: Send email on failure
      if: failure()
      uses: dawidd6/action-send-mail@f27e7f6b8db930130dd85af56efca5063d88e108
      with:
        server_address: smtp.gmail.com
        server_port: 465
        username: ${{ secrets.NOTIFY_EMAIL_USERNAME }}
        password: ${{ secrets.NOTIFY_EMAIL_PASSWORD }}
        subject: ${{ github.job }} job of ${{ github.repository }} has ${{ job.status }}
        body: "${{ github.job }} job in worflow ${{ github.workflow }} of ${{ github.repository }} has ${{ job.status }}. More info: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"
        to: emailthatshouldbenotified@examplemail.com
        from: "Jira & Confluence backup schedule"


```
