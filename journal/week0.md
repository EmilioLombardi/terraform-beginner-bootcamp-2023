# Terraform Beginner Bootcamp 2023 - Week 0

- [Semantic Versioning](#semantic-versioning)
- [Install the Terraform CLI](#install-the-terraform-cli)
  * [Considerations with the Terraform CLI changes](#considerations-with-the-terraform-cli-changes)
  * [Considerations for Linux Distributions](#considerations-for-linux-distributions)
  * [Refactoring into Bash Scripts](#refactoring-into-bash-scripts)
    + [Shebang](#shebang)
    + [Execution Considerations](#execution-considerations)
    + [Linux Permissions Considerations](#linux-permissions-considerations)
- [Gitpod Lifecycle](#gitpod-lifecycle)
- [Working with Env Vars](#working-with-env-vars)
  * [Setting and Unsetting Env Vars](#setting-and-unsetting-env-vars)
  * [Printing Vars](#printing-vars)
  * [Scoping of Env Vars](#scoping-of-env-vars)
  * [Persisting Env Vars in Gitpod](#persisting-env-vars-in-gitpod)
- [AWS CLI installation](#aws-cli-installation)
- [Terraform Basics](#terraform-basics)
  * [Terraform Registry](#terraform-registry)
  * [Terraform Console](#terraform-console)
    + [Terraform Init](#terraform-init)
    + [Terraform Plan](#terraform-plan)
    + [terrform Apply](#terrform-apply)
    + [Terraform Destory](#terraform-destory)
    + [Terraform Lock Files](#terraform-lock-files)
    + [Terraform State Files](#terraform-state-files)
    + [Terraform Directory](#terraform-directory)
    + [S3 bucket setup configuration issue](#s3-bucket-setup-configuration-issue)
- [Potential issues with Terraform Cloud Login and Gitpod Workspace](#potential-issues-with-terraform-cloud-login-and-gitpod-workspace)
    + [Forgetting to create new issue](#forgetting-to-create-new-issue)





## Semantic Versioning 

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

### Gitpod Lifecycle

We need to be careful when using the Init because it will not rerun if we restart an existing workspace.

https://www.gitpod.io/docs/configure/workspaces/tasks

## Working with Env Vars

We can list out all ENviroment Variable (Env Vars) using the `env` command

We can filter specific env vars using grep eg. `env | grep AWS_ `

### Setting and Unsetting Env Vars

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

### Printing Vars

We can print an env var using echo eg. `echo $HELLO`

### Scoping of Env Vars

When you open up new bash terminals in VSCode it will not be aware of env vars that you have set in another window.

If you want to Env Vars to persist across all future bash terminals that are open you need to set env vars in your bash profile. eg. `.bash_profile`

### Persisting Env Vars in Gitpod

We can persist env vars into gitpod by storing them in Gitpod Secrets Storage.

```
gp env HELLO='world'
```

All future workspaces launched will set the env vars for all bash terminals opened in those workspaces.

You can also set env vars in the `.gitpod.yml` but this can only contain non-senstive env vars.

## AWS CLI installation 

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


## Terraform Basics

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

#### S3 bucket setup configuration issue

When setting up an S3 bucket you must make sure that the bucket name does not conflict with AWS's bucket naming rules. This may be the case as the bucket name generated is random. 

To work around this issue we need to define the configuration, which will make sure that the generated bucket name does not conflict with the Bucket naming Rules

```hcl
 resource "random_string" "bucket_name" {
  lower = true
  upper = false
  length   = 32
  special  = false
}
```

## Potential issues with Terraform Cloud Login and Gitpod Workspace

When attempting to `terraform login` it will launch in bash, a wiswig view to generate a token. However, it may not work in Gitpod VsCode browser. I personally did not encounter this issue myself. Here is the workaround just incase we run in to this issue

The workaround is to manually generate a token in Terraform Cloud

```
https://app.terraform.io/app/settings/tokens?source=terraform-login
```

Then create open the file manually here:

```sh
touch /home/gitpod/.terraform.d/credentials.tfrc.json
open /home/gitpod/.terraform.d/credentials.tfrc.json
```

Provide the following code (replace your token in the file):
Then open the file

```json
{
	"credentials": {
		"app.terraform.io": {
			"token": "YOUR-TERRAFORM-CLOUD-TOKEN"
		}
	}
}
```

We have automated this workaround with the following bash script [bin/generate_tfrc/credentials](bin/generate_tfrc_credentials)

#### Forgetting to create new issue

If we for whatever reason forget to make a new issue while working in gitpod. Here is what we need to do:
On Github create new issue and then create new branch
in the VsCode CLI type `Git add .` then `git stash save` this will save the working directory and index state WIP on main.
Type `git stash list` then `git fetch` 
`git checkout` (Write the branch name after checkout) this will set up to track and switch to a new branch
Type `Git pull` (Already up to date.) is what responde you should get back
Then Type `git stash apply` this will then tell toy what chanages is to be commted and changes not staged for commit
