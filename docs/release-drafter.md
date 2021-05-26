## Release Drafter

![release-drafter](assets/release-drafter.png)

See [release-drafter/release-drafter](https://github.com/release-drafter/release-drafter#readme) for advance usage guide.

**Template and config**

- Template: [workflow-templates/release-drafter.yml](https://github.com/soramitsukhmer/.github/blob/main/workflow-templates/release-drafter.yml)
- Config: [workflow_configs/release-drafter.yml](https://github.com/soramitsukhmer/.github/blob/main/docs/workflow_configs/release-drafter.yml)

**Release drafter labels**

Any pull request that use the following labels will mark as **Features**
  * `feature`
  * `enhancement`

Any pull request that use the following labels will mark as **Bug Fixes**
  * `fix`
  * `bugfix`
  * `bug`
  * `hotfix`

Any pull request that use the following labels will mark as  **Maintenance**
  * `chore`

> You can exclude any pull request from the changelog by adding `skip-changelog` label.

There are additional labels you can use for helping **Release Drafter** to generate version number for you.

| label         | description         | color   |
| ------------- | ------------------- | ------- |
| release-major | Tag a major release | #71ed7e |
| release-minor | Tag a minor release | #d5f990 |
| release-patch | Tag a patch release | #98f9cc |
