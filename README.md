# Python Flask App Demo for GitOps
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fkcl-lang%2Fflask-demo.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fkcl-lang%2Fflask-demo?ref=badge_shield)


## CI Scripts

Config yout secrets including `DOCKER_USERNAME`, `DOCKER_PASSWORD` and `DEPLOY_ACCESS_TOKEN` and update your application code to trigger automation build and deploy.

```yaml
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2
      
      - name: Docker Login
        uses: docker/login-action@v1.10.0
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
          logout: true

      # Runs a set of commands using the runners shell
      - name: build image
        run: |
          make image
          docker tag flask_demo:latest ${{ secrets.DOCKER_USERNAME }}/flask_demo:${{ github.sha }}
          docker push ${{ secrets.DOCKER_USERNAME }}/flask_demo:${{ github.sha }}

      - name: Trigger CI
        uses: InformaticsMatters/trigger-ci-action@1.0.1
        with:
          ci-owner: kcl-lang
          ci-repository: flask-demo-kcl-manifests
          ci-ref: refs/heads/main
          ci-user: peefy
          ci-user-token: ${{ secrets.DEPLOY_ACCESS_TOKEN }}
          ci-name: CI
          ci-inputs: >-
            image=${{ secrets.DOCKER_USERNAME }}/flask_demo
            sha-tag=${{ github.sha }}
```


## License
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fkcl-lang%2Fflask-demo.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fkcl-lang%2Fflask-demo?ref=badge_large)