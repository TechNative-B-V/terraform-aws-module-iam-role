# Terraform AWS [iam-role]

This module implements a module to create roles and easily define trust relationships.

[![](we-are-technative.png)](https://www.technative.nl)

## How does it work

### First use after you clone this repository or when .pre-commit-config.yaml is updated

Run `pre-commit install` to install any guardrails implemented using pre-commit.

See [pre-commit installation](https://pre-commit.com/#install) on how to install pre-commit.

## Usage

A mostly complete example is demonstrated below.


```hcl
module "dreamlines_website_cicd_build_role" {
  source = "git@github.com:TechNative-B-V/terraform-aws-module-iam-role?ref=HEAD" # change to commit or version later

  role_name = "website_stack_role"
  role_path = "/website_stack/ci_cd/"

  aws_managed_policies      = [ "AdministratorAccess" ]
  customer_managed_policies = {
    "website_codebuild_cloudwatch": jsondecode(data.aws_iam_policy_document.website_codebuild_cloudwatch.json)
  }

  trust_relationship = {
    "codebuild" : { "identifier" : "codebuild.amazonaws.com", "identifier_type" : "Service", "enforce_mfa" : false, "enforce_userprincipal" : false, "external_id" : null, "prevent_account_confuseddeputy" : false }
  }
}

data "aws_iam_policy_document" "website_codebuild_cloudwatch" {
  statement {
    actions = ["logs:CreateLogStream", "logs:PutLogEvents"]

    resources = [ "arn:${data.aws_partition.current.id}:logs:${data.aws_region.current.name}:${data.aws_caller_identity.current.account_id}:log-group:/aws/codebuild/website_stack_website_*" ]
  }
}
```

<!-- BEGIN_TF_DOCS -->
<!-- END_TF_DOCS -->
