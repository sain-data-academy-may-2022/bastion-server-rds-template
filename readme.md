# EC2 bastion server and a RDS

This is an EC2 bastion server and a RDS instance setup for the Sainsburys data academy final project's teams.

## Requirements

- docker
- docker-compose 

## Installation

- Create an environment file `deploy/.sainsburys.env` and put your fresh `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN` in it. (you can find these credentials with command  `cat ~/.aws/credentials`)
```sh
aws-azure-login --profile bootcamp-sandbox --mode=gui
```

```sh
AWS_ACCESS_KEY_ID=<your-aws-access-key-id>
AWS_SECRET_ACCESS_KEY=<your-aws-secret-access-key>
AWS_SESSION_TOKEN=<your-aws-session-token>
```
- Create a file `deploy/terraform.tfvars` and put your desired values in it: 
```py
db_username = "<your-db-root-user-name>"
db_password = "<your-db-root-password>"
db_name     = "<your-db-name>"
```
- run:
```bash
make tf-init
```

## Create

```python
# after every change make sure the syntax of the Terraform commands are right
make tf-validate

# read the plan to see what are going to be created
make tf-plan

# read the plan again and if this is what you want, confirm it
make tf-apply
```

## Check Connectivity

- Connect to the bastion server:
```sh
aws ssm start-session --target [instance_id] --profile [name_of_profile] --region eu-west-1
```
- Make sure you can ping the rds database to see whether the network configuration is ok:

```sh
telnet <your-rds-db-endpoint> <your-db-port-number>
```

## Usage

- run (it will do port-forwarding from your machine to the bastion server - keep this terminal open):
```sh
aws ssm start-session \
    --target <ec2-instance-id> \
    --profile <name_of_profile> \
    --document-name AWS-StartPortForwardingSessionToRemoteHost \
    --parameters '{"host":["<your-rds-db-endpoint>"],"portNumber":["<your-db-port-number>"], "localPortNumber":["<your-local-port-number>"]}'
    --region eu-west-1
```
- Connect to the db using your preferred db tool, using `localhost` as the endpoint, `<your-local-port-number>` from the previous command as the db-port and your defined credentials in the Installation section.

## Destroy 

```python
# destroy your created infra
make tf-destroy
```
