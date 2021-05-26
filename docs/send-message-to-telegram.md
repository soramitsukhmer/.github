# Send message to Telegram

Here and example of how you could notify to `Soramitsu-alerts` telegram group.

**Required secrets**:
- `${{ secrets.TELEGRAM_TO }}`
- `${{ secrets.TELEGRAM_TOKEN }}`

**Workflow**:

This example uses the [workflow-templates/frontend-docker-release.yml](https://github.com/soramitsukhmer/.github/blob/main/workflow-templates/frontend-docker-release.yml) workflow.

```yml
name: Release Docker Image
on:
  release:
    types: [published]
jobs:
  build:
    # ...

  notify:
      name: Notify to Telegram
      runs-on: [self-hosted, linux, x64]
      needs: [build]
      steps:
      - name: Release notification
        uses: appleboy/telegram-action@master
        with:
          to: ${{ secrets.TELEGRAM_TO }}
          token: ${{ secrets.TELEGRAM_TOKEN }}
          format: markdown
          message: "**${{github.repository}}**: Release **${{ github.event.release.tag_name }}** published by **${{github.actor}}**"
```
