# Terraform Beginner Bootcamp 2023

## Semantic Versioning :mage:

This project is going to utilise semantic versioning for its tagging
[semver.org](https://semver.org/)

The general format:

 **MAJOR.MINOR.PATCH**, eg. `1.0.1`

- **MAJOR** version when you make incompatible API changes
- **MINOR** version when you add functionality in a backward compatible manner
- **PATCH** version when you make backward compatible bug fixes

## Install the Terraform CLI

### Considerations with the Terraform CLI changes
The terraform CLI installation instructions have changed due to gpg keyring changes. So we needed refer to the latest install CLI instructions via Terraform Documentation and change the scripting  

[Install Terraform CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)


### Considerations for Linux Distributions

This project is built against Ubunutu.
Please consider checking your Linux Distribution and change accordingly to distirbution needs 

[How to check OS Version in Linux](https://www.cyberciti.biz/faq/how-to-check-os-version-in-linux-command-line/)

Example of checking OS Version:

```

$cat /etc/os-release

PRETTY_NAME="Ubuntu 22.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.3 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
```

### Refactoring into Bash Scripts

While fixing the Terraform CLI gpg depreciation issues we notices that the bash scripts steps were a considerable amount more code. So we decided to create a bash script to install the Terraform CLI.

This bash script is located here: [.bin/install_terraform_cli](./bin/install_terraform_cli)

- This will keep the Gitpod Task File ([.gitpod.yml](.gitpod.yml)) tidy.
- This will allow us an easier time to debug and excecute Terraform CLI install manually
- This will allow better portability for other projects that need to install Terraform CLI

#### Shebang

A Shebang (pronounced Sha-bang) tells the bash script what program that will interpret the script eg. `#!/bin/bash`

ChatGPT recommended this format for bash: `#!/usr/bin/env bash`

- For portability for different OS distributions
- Will search the user's PATH for the bash executable

https://en.wikipedia.org/wiki/Shebang_(Unix)

#### Execution Considerations
When executing the bash script we can use the `./` shorthand notiation to execute the bash script. 

eg. `./bin/install_terraform_cli` 

IF we are using a script in .gitpod.yml we need to point the script to a program to interpret it.

eg. `source ./bin/install_terraform_cli`

#### Linux Permissions Considerations 

In order to make our bash scripts executable we need to change linux permissions for the fix to be executable at the user mode.

```sh
chmod u=x ./bin/install_terraform_cli
```

```sh
chmod 744 ./bin/install_terraform_cli
```

https://en.wikipedia.org/wiki/Chmod

### Github Lifecycle (Before, Init, Command)

We need to be careful when using the Init because it will not rerun if we restart an existing workspace.

https://www.gitpod.io/docs/configure/workspaces/tasks

### Working with Env Vars

We can list out all ENviroment Variable (Env Vars) using the `env` command

We can filter specific env vars using grep eg. `env | grep AWS_ `

#### Setting and Unsetting Env Vars

In the terminal we can set using `export HELLO='world`

In the terminal we can unset using `unset HELLO`

We can set env var temporarily when just running a command 

```sh
HELLO='world' ./bin/print_message
```
Within a bash script we can set env without writing export eg.

```sh
#1/usr/bin/env bash

HELLO='world'

echo $HELLO
```

#### Printing Vars

We can print an env var using echo eg. `echo $HELLO`

#### Scoping of Env Vars

When you open up new bash terminals in VSCode it will not be aware of env vars that you have set in another window.

If you want to Env Vars to persist across all future bash terminals that are open you need to set env vars in your bash profile. eg. `.bash_profile`

#### Persisting Env Vars in Gitpod

We can persist env vars into gitpod by storing them in Gitpod Secrets Storage.

```
gp env HELLO='world'
```

All future workspaces launched will set the env vars for all bash terminals opened in those workspaces.

You can also set env vars in the `.gitpod.yml` but this can only contain non-senstive env vars.

### AWS CLI installation 

AWS CLI is installed for this project via the bash script [`./bin/install_aws_cli`](./bin/install_aws_cli)

[Getting Started Install (AWS CLI)]https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

[AWS CLI Env Vars]https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html

We can check if our AWS credentials is configured correctly by running the followering commands

```sh
 aws sts get-caller-identity
```

If it is succesful you should see a json payload return that looks like this (Example):

```json
{
    "UserId": "AIDASFU7LTGCP1EXAMPLE",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/terraform-beginner-bootcamp"
}
```
We'll need to generate AWS CLI credentials from IAM User in order to use the AWS CLI this is important.

Also make sure that Yaml files are correct, as once you stop gitpod, it will not allow you to access. YAML validator can confirm if Yaml file is valid.


## terraform Basics

### Terraform Registry

Terraform sources their providers and modules from the Terraform registory which is located at [registry.terraform.io](https://registry.terraform.io/)

- **Providers** is an interface to APIs that will allow you to create resources in terraform.
- **Modules** are a way to make large amounts of terraform code modular, portable and sharable.

[Random terraform provider](https://registry.terraform.io/providers/hashicorp/random)

## Terraform Console

We can see a list of all the Terraform commands by simply typing `terraform`


#### Terraform Init

At the start of a new terraform project we will run `terraform init` to download the binaries for the terraform providers that we'll use in this project.

#### Terraform Plan

`terraform plan`

This will generate out a changeset, about the state of our infrastructure and what will be changed.

We can output this changeset ie. "plan" to be passed to an apply, but often you can just ignore outputting.

#### terrform Apply

`terraform apply`

This will run a plan and pass the changeset to be executed by terraform. Apply should prompt yes or no.

If we want to automatically approve an apply we can provide the auto approve flag eg. `terraform apply --auto-approve`

#### Terraform Destory

This will destory resources eg. If you have a S3 bucket and you want to destory it, entering the command `terraform destroy` and then entering `yes` with permantly delete the bucket

You can also use the auto approvr flag to skip the approve prompt eg. `terraform apply --auto-approve`

#### Terraform Lock Files

`.terraform.lock.hcl` contains the locked versioning for the providers or modules that should be used with this project.

The Terraform Lock File **should be committed** to your Version Control System (VSC) eg. Github

#### Terraform State Files

`.terraform.tfstate` contains information about the current state of your infrastructure.

This file **should not be committed** to your VCS.

This file can contain sensentive data.

If you lose thise file, you lose knowing the state of your infrastructure.

`.terraform..tfstate.backup` is the previous state file state.

#### Terraform Directory

`.terraform` directory contains binaries of terraform providers.

