## Frontend: Release Docker Image

![frontend-docker-release](assets/frontend-docker-release.png)

The **Frontend: Release Docker Image** workflow uses the official [docker/build-push-action](https://github.com/docker/build-push-action#readme).

**Template and config**

- Template: [workflow-templates/frontend-docker-release.yml](https://github.com/soramitsukhmer/.github/blob/main/workflow-templates/frontend-docker-release.yml)

**FAQs**

- Git issues when run "docker buildx build" on "self-hosted" runner, docker/build-push-action#161
