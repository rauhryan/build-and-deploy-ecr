# build-and-deploy-ecr
This github action builds a docker image from a dockerfile, healthchecks it, and pushes it to ECR in compliance with GLG naming conventions

## Requirements

This action requires certain things to be configured in your repo:

1. You must have a **dockerfile** in the root directory of your repo.
2. You must have the following **secrets** present in your repository. These should be added automatically by a different process.
    1. `ECR_URI`
    2. `ECR_AWS_ACCESS_KEY_ID`
    3. `ECR_AWS_SECRET_ACCESS_KEY`
3. This action was developed against the `ubuntu-20.04` github actions environment, and it may not work correctly in a different environment.

## Configuration

| Input | Description | Default |
|-------|-------------|---------|
| access_key_id | An AWS Access Key ID | **REQUIRED** |
| build_config | Config file used during `docker build` usually `postinstall` (example `webpack.config.js`) to install your app. Do not build your app in `npm install`. | `""` |
| deploy | Whether to push the image to ECR after building it | `"true"` |
| dockerfile | Custom Dockerfile path to use to build your image (`prod.Dockerfile`) | `Dockerfile` |
| ecr_uri | The URI of the ECR repository to push to | **REQUIRED** |
| env_file | File containing environment variables required for app to run and pass healthcheck | `""` |
| github_ssh_key | An SSH Private Key with access to any private repos you need | `""` |
| healthcheck | A healthcheck path, like /healthcheck | `/healthcheck` |
| port | The port the server listens on | `3000` |
| secret_access_key | An AWS Secret Access Key | **REQUIRED** |

### Config Notes

* `env_file` - The format for this file is `NAME=value` as described in the [docker docs](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file).
  Notice the lack of the `export` keyword.

## Example Usage

```yml
name: Build Image and Push to ECR
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-20.04
    steps:
    - uses: actions/checkout@main
    - uses: glg-public/build-and-deploy-ecr@main
      with:
        ecr_uri: ${{secrets.ECR_URI}}
        access_key_id: ${{secrets.ECR_AWS_ACCESS_KEY_ID}}
        secret_access_key: ${{secrets.ECR_AWS_SECRET_ACCESS_KEY}}
```

You can optionally disable deploying, and have this action only build your image:

```yml
name: Build Image and Do Not Push to ECR
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-20.04
    steps:
    - uses: actions/checkout@main
    - uses: glg-public/build-and-deploy-ecr@main
      with:
        ecr_uri: ${{secrets.ECR_URI}}
        access_key_id: ${{secrets.ECR_AWS_ACCESS_KEY_ID}}
        secret_access_key: ${{secrets.ECR_AWS_SECRET_ACCESS_KEY}}
        deploy: false
```

## Example with Private Dependencies

```yml
name: Build Image and Push to ECR
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-20.04
    steps:
    - uses: actions/checkout@main
    - uses: glg-public/build-and-deploy-ecr@main
      with:
        ecr_uri: ${{secrets.ECR_URI}}
        access_key_id: ${{secrets.ECR_AWS_ACCESS_KEY_ID}}
        secret_access_key: ${{secrets.ECR_AWS_SECRET_ACCESS_KEY}}
        github_ssh_key: ${{secrets.GITHUB_SSH_KEY}}
```
