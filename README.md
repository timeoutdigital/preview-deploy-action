preview-deploy-action
=============
Interacts with kubernetes clusters calling `helm` commands. Integrates support for **AWS EKS**.

## Usage

### EKS Example

```yml
name: CI

on:
  - push

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-west-1  

      - name: Deploy
        uses: timeoutdigital/preview-deploy-action@master
        env:
          KUBE_CONFIG_DATA: ${{ secrets.KUBE_CONFIG_DATA }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}   
        with:
          action: install
          app_name: gp-web
          domain_name: "digital.timeout.com"
          imageTag: ${{ github.sha }} 
          graffitiSecret: ${{ secrets.GRAFFITI_CLIENT_SECRET }} 
          accountId: ${{ secrets.AWS_ACCOUNT_ID }}
```

## Config

### Secrets

One or more **secrets** needs to be created to store cluster credentials. (see [here](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) for help on creating secrets). 

#### Basic
- **KUBE_CONFIG_DATA**: A `base64` representation of `~/.kube/config` file.
- **AWS_ACCOUNT_ID**: Account id of AWS account, used for resolving ECR repo.
- **GRAFFITI_CLIENT_SECRET**: Used to set the secret used by gp-web

##### Example
```bash
cat ~/.kube/config | base64 | pbcopy # pbcopy will copy the secret to the clipboard (Mac OSX only)
```

#### EKS
- **KUBE_CONFIG_DATA**: Same as Basic configuration above.

- **AWS_ACCESS_KEY_ID**: AWS_ACCESS_KEY_ID of a IAM user with permissions to access the cluster.

- **AWS_SECRET_ACCESS_KEY**: AWS_SECRET_ACCESS_KEY of a IAM user with permissions to access the cluster.

Make sure your users has the proper IAM permissions to access your cluster and that its configured inside kubernetes (more info [here](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html)).

## Outputs

- **result**: Output of the `helm` command.
