# Workshop

This README.md is intended to act as a hands-on guide for implementing StackHawk's DAST scanner HawkScan.

## Step One: Fork, Clone, Build
- [ ] Fork [Vuln_Node_Express](https://github.com/kaakaww/vuln_node_express)
- [ ] Clone Vuln_Node_Express
- [ ] Navigate to the newly created vuln_node_express directory
- [ ] Build and Run the app with the Docker Compose command listed in the Vuln_Node_Express READ.me

## Step Two: Local Scan
- [ ] Create an and save an API key via the StackHawk Web Platform
- [ ] Create an application in the StackHawk Web Platform and save the `applicationId`
- [ ] Create a `stackhawk.yml` file at the root of your recently cloned project directory
- [ ] Add the following basic configuration to your `stackhawk.yml` file, while supplying your `applicationId`

```YAML
app:
  applicationId: xxxxx-XXXXXX-xxxx-XXXX-XXXxxxxXXX
  env: Development
  host: http://localhost:3000
```
- [ ] Supply your API_KEY to the following Docker command and run the command

```bash 
docker run --name hawkscan --network host -e API_KEY=hawk.xxxxXXXXxxxxXXXXXxxxx --rm -v $(pwd):/hawk:rw -it stackhawk/hawkscan:latest
```


## Step Three: CI/CD with Github Actions
- [ ] Navigate to the settings tab from your forked vlun_node_express repository
- [ ] Select secrets and save your StackHawk API key as `HAWK_API_KEY`
- [ ] Create at `.github/workflows/main.yml` in your at the root of your project directory
- [ ] Add the following definitions to your Github Actions `main.yml` file

```YAML
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build and run Vuln_node_express
        run: docker-compose up --build --detach

      - name: Wait for Host
        uses: smurfpandey/wait-for-it@v1.0
        with:
          host: localhost
          port: 3000
          timeout: 10

      - name: Run StackHawk
        uses: stackhawk/hawkscan-action@v1.3.0
        with:
          apiKey: ${{ secrets.HAWK_API_KEY }}
          dryRun: false
```