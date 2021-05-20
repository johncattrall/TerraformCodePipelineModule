# TerraformCodePipelineModule

This module defines a *pipeline* which can deliver a *payload*. Specifically, an importable, Terraform defined, CodePipeline module used to deliver Terraform *payloads* to and AWS account, which are also typically considered as *workloads* (this is out of the scope of landing zone solutions).

*Pipelines* created by this module will execute when new commits are pushed to the corresponding repo & branch it is connected to.


This project is intended to integrate with github

To use it, you might incorporate it as follows:

```terraform
terraform {
  backend "s3" {}
}

locals {
  tags = {
    Owner       = "jcattra"
    ProjectName = "${var.repository_name}-Pipeline"
  }
}

module "pipeline" {
  source          = "github.com/$GITHUB_ORG/TerraformCodePipelineModule"
  tags            = local.tags
  region          = "us-west-2"
  repository_name = var.repository_name
}
```

The pipeline module expects that your *payload* contains the following directory structure and key files, for ease of automation.

```
buildspecs/
  buildspec-apply.sh - evaluated on pipeline run, describes the apply stage
  buildspec-apply.yml - interpolated on pipeline deploy, describes the apply build step
  buildspec-plan.sh - evaluated on pipeline run, describes the plan stage
  buildspec-plan.yml - interpolated on pipeline deploy, describes the plan build step
terraform
  payload - holds the terraform to provision
  pipeline - holds a pipeline definition (see above code snippets or example payload repo)
```

This repo also includes terraform to bootstrap the backend storage for a terraform envionment, and can be ran from the `bootstrap` directory by executing `terraform apply` in an authenticated shell. This will provision Terraform state storage as described @ https://www.terraform.io/docs/backends/types/s3.html.



# List of Variables Supported by this Terraform Module

```
region (the aws region in which to deploy)
repository_name (the name of the github repository to integrate your pipeline with)
tags (the tags to use on your resources.)
```
