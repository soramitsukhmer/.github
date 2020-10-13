# CD (continuous delivery/continuous deployment)

### What are the differences between continuous integration, continuous delivery, and continuous deployment?

See https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment

#### Continuous delivery

Continuous delivery is an extension of continuous integration to make sure that you can release new changes to your customers quickly in a sustainable way. This means that on top of having automated your testing, you also have automated your release process and you can deploy your application at any point of time by clicking on a button.

In theory, with continuous delivery, you can decide to release daily, weekly, fortnightly, or whatever suits your business requirements. However, if you truly want to get the benefits of continuous delivery, you should deploy to production as early as possible to make sure that you release small batches that are easy to troubleshoot in case of a problem.

#### Continuous deployment

Continuous deployment goes one step further than continuous delivery. With this practice, every change that passes all stages of your production pipeline is released to your customers. There's no human intervention, and only a failed test will prevent a new change to be deployed to production.

Continuous deployment is an excellent way to accelerate the feedback loop with your customers and take pressure off the team as there isn't a Release Day anymore. Developers can focus on building software, and they see their work go live minutes after they've finished working on it.

## Usage

To create a deployment, you need to add the following `deployment` step to your existing workflow. e.g. [workflow-templates/frontend-docker-release.yml](https://github.com/soramitsukhmer/.github/blob/main/workflow-templates/frontend-docker-release.yml).

```yml
# frontend-docker-release.yml

name: Release Docker Image

on:
  release:
    types: [published]

jobs:
  build:
    # build application before create a deployment request
    #
  deployment:
    name: Deployment
    runs-on: ubuntu-latest
    needs: [build]
    steps:
      - name: Prepare environment
        id: deployment_config
        run: |
          echo ::set-output name=task::deploy

          if [ "${{ github.event.release.prerelease }}" = "true" ]; then
            echo ::set-output name=environment::staging
          else
            echo ::set-output name=environment::production
          fi
      - name: Create deployment on ${{ steps.deployment_config.outputs.environment }}
        uses: octokit/request-action@v2.x
        with:
          route: POST /repos/:repository/deployments
          repository: ${{ github.repository }}
          ref: ${{ github.event.release.tag_name }}
          task: ${{ steps.deployment_config.outputs.task }}
          environment: ${{ steps.deployment_config.outputs.environment }}
        env:
          GITHUB_TOKEN: ${{ secrets.GH_TOKEN }}
```

Please set the `${{ secrets.GH_TOKEN }}` secret with your **Personal Access Token** in order to make this work.

```yml
# deliverybot-deploy.yml

name: Deployment

on: [deployment]

jobs:
  deployment:
    name: Deploy ${{ github.event.deployment.environment }}
    runs-on: ubuntu-latest
    steps:
      - name: Deployment pending
        uses: "deliverybot/deployment-status@master"
        with:
          state: "pending"
          token: ${{ github.token }}

      # Start deployment
      - name: Deploy using SSH to ${{ github.event.deployment.environment }}
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          port: ${{ secrets.SSH_PORT }}
          script: |
            echo "task - ${{ github.event.deployment.task }}"
            echo "environment - ${{ github.event.deployment.environment }}"
      # End deployment

      - name: Deployment success
        if: success()
        uses: "deliverybot/deployment-status@master"
        with:
          state: "success"
          token: ${{ github.token }}

      - name: Deployment failure
        if: failure()
        uses: "deliverybot/deployment-status@master"
        with:
          state: "failure"
          token: ${{ github.token }}
```
