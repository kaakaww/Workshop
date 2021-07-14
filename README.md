# Workshop

This README.md is intended to act as a hands-on guide for implementing StackHawk's DAST scanner HawkScan.

## Step One: Fork, Clone, Build
- Fork [Vuln_Node_Express](https://github.com/kaakaww/vuln_node_express)
- Build and Run the app with the Docker Compose command listed in the Vuln_Node_Express READ.me

## Step Two: Local Scan
- Create an application in the StackHawk Web Platform and copy the `applicationId`
- Create a `stackhawk.yml` file at the root of your recently cloned project directory
- Add the following basic configuration to your `stackhawk.yml` file supplying your `applicationId`

```YAML
app:
  applicationId: xxxxx-XXXXXX-xxxx-XXXX-XXXxxxxXXX
  env: Development
  host: http://localhost:3000
```
- Run the following Docker command
```bash 
docker run --name hawkscan --network host -e API_KEY=${API_KEY} --rm -v $(pwd):/hawk:rw -it stackhawk/hawkscan:latest
```


## Step Three: CI/CD with Github Actions