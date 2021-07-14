# Workshop

This README.md is intended to act as a hands-on guide for implementing StackHawk's DAST scanner HawkScan.

**Resources**
- [StackHawk Documentation](https://docs.stackhawk.com/)
- [Github Action](https://github.com/marketplace/actions/stackhawk-hawkscan-action)

## Step One: Fork, Clone, Build
- [ ] Fork [Vuln_Node_Express](https://github.com/kaakaww/vuln_node_express)
- [ ] Clone Vuln_Node_Express
- [ ] Navigate to the newly created vuln_node_express directory
- [ ] Build and Run the app with the Docker Compose command listed in the Vuln_Node_Express README.md

## Step Two: Local Scan
- [ ] Create and save an API key via the StackHawk Web Platform
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
export HAWK_API_KEY=hawk.xxxxXXXXxxxxXXXXxxxx
```
```bash
docker run --name hawkscan --network host -e HAWK_API_KEY --rm -v $(pwd):/hawk:rw -t stackhawk/hawkscan:latest
```


## Step Three: CI/CD with Github Actions
- [ ] Navigate to the settings tab from your forked vlun_node_express repository 
- [ ] Select secrets and save your StackHawk API key as `HAWK_API_KEY`
- [ ] Create a `.github/workflows/main.yml` file at the root of your project directory
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

## Step Four: Vuln Thresholds & Code Scanning Alerts
- [ ] Add `hawk.failureThreshold` to your `stackhawk.yml` file

```YAML
app:
  applicationId: xxxxx-XXXXXX-xxxx-XXXX-XXXxxxxXXX
  env: Development
  host: http://localhost:3000
```
- [ ] Add `codeScanningAlerts` and `githubToken` to the Run Stackhawk step in your Github Workflows `main.yml`

```YAML
- name: Run StackHawk
        uses: stackhawk/hawkscan-action@v1.3.0
        with:
          apiKey: ${{ secrets.HAWK_API_KEY }}
          codeScanningAlerts: true
          githubToken: ${{ github.token }}
          dryRun: false
```