# Terraform in an Instant
Terraform is a popular language used for defining and provisioning Infrastructure as Code (IaC) that supports the CI/CD workflow.

## Learning goals
At the completion of this tutorial, you will be able to:
* Practice using common Terraform commands
* Label the key components of a Terraform configuration
* Describe key use cases and benefits of Terraform

## Prerequisites
Downloads of Terraform are available for most target systems by either package manager or binary. Visit https://terraform.io to see the available list of supported systems.

The Terraform configuration file you will run in this tutorial requires a Docker daemon to be running locally. Therefore, you will need to install and configure it separately. While the Docker topic is beyond the scope of this tutorial, you can follow this [link]("https://docs.docker.com/get-docker/") to get started. Once installed, you should run the Docker daemon and keep it running throughout this tutorial.

## Organize the Terraform project
Most people new to Terraform find it easiest to create a new directory on their local machine and create Terraform configuration files inside it.

```bash
$ mkdir terraform-demo
$ cd terraform-demo

# This is the file where you will configure Terraform 
$ touch main.tf
```

## Configure `main.tf`
Open `main.tf` in a text editor and paste the adjacent Terraform code into the file.
```bash
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```
## The `terraform {}` block 
The first block in `main.tf` is the `terraform` block. This special block applies settings specific to how Terraform should run. For example, the `required_providers` block tells Terraform to use the Docker provider that lives in the online Terraform registry, under ["kreuzwerker/docker"](https://registry.terraform.io/providers/kreuzwerker/docker/2.16.0).    
```bash
...
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
...
```
> To reduce the maintenance cost for you as a Terraform developer, we recommend using providers found in the online Terraform registry whenever possible. Many of the providers found there are actively maintained.
 
While other types of blocks in Terraform perform similar property-setting functions, there is no other block type that configures how Terraform itself should run.

> Check Terraform installation by navigating to the directory where `main.tf` lives. Then, at a command prompt/terminal, run `terraform -v`. A successful call with `-v` will output something similar to this: `Terraform v1.1.6`.

## Run `terraform init`
```bash
$ terraform init
```
The `terraform init` command should be the first in a set of commands that builds your infrastructure. The `init` command performs essential functions. First, it initializes the current directory to the working directory. It initializes the components needed for the infrastructure, including the installation of all referenced modules, all declared providers, and the configuration of backend services. The `init` command also upgrades all the modules and providers you declare. For example, suppose you need to upgrade and reboot the entire video serving capacity the likes of Netflix. In that case, it's pretty clear how leveraging an upgrade function in the IaC solution can save thousands of hours and substantially reduce costly errors, automated at scale. 

### Run `terraform plan`
```bash
$ terraform plan
```
The `terraform plan` command previews the configuration defined in `main.tf`. Terraform's declarative programming style makes it possible to introduce configuration errors easily. With nothing intrinsic in the language to debug malformed infrastructure, the `plan` command helps pre-empt such issues.

> There should be no reason to leave `terraform plan` out of your IaC strategy, particularly in production environments.

### Run `terraform apply`
The `terraform apply` command builds the infrastructure you defined in your Terraform configuration file(s). If you ran `terraform plan` without errors, you could expect none with this command. Of the three commands covered, `terraform apply` will take the longest time to run. The `apply` command builds, updates, and destroys the infrastructure based on your definition and configuration. When asked to confirm the actions, type "yes".

### Browse to `0.0.0.0:80`
After successfully running `terraform apply`, open a browser and enter the address `0.0.0.0:80`. You should see a welcome page for an nginx server. The `main.tf` command retrieved a provider from the Terraform registry, started a backend container service, deployed a server with that container, and finally configured the server and started it.

### Run `terraform destroy`
The last command covered in this tutorial is `terraform destroy`. This command cleanly reverses all the infrastructure implemented with `terraform apply`. Everything provisioned can be undone with `destroy`. This command will also require confirmation by typing "yes".

## Wrap up and next steps
In this tutorial we introduced the four basic commands in Terraform: `init` `plan` `apply` `destroy`. We also introduced a project structure that can be used for small Terraform configurations. And finally, we introduced an nginx server running locally at port 80, as defined in `main.tf`.

For the next step, you can continue developing with a Docker provider by visiting this [tutorial](https://learn.hashicorp.com/collections/terraform/docker-get-started).

Best of luck and happy Terraforming!
