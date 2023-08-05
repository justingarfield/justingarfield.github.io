---
layout: post
title: Oracle Cloud Free Tier Setup w/ Terraform
date: 2023-07-25 00:00:00 -0500
categories: cloud iac
tags: cloud oracle terraform
excerpt: Oracle Cloud Free Tier Setup w/ Terraform
---

## Overview

I've been wanting to check-out the Oracle Cloud Infrastructure (OCI) Free Tier offering for some time now, so that I can have more data-points to compare against Microsoft Azure and Amazon AWS.

I took a few hours today to sign-up for an account, poke-around the documentation, and provision a couple of resources using Terraform; putting me in a decent spot to continue my learning in this cloud environment over time.

In this post we:

* Sign-up for the Oracle Cloud - Free Tier offering
* Introduce to the OCI Terraform Provider
* Set-up a fresh baseline Terraform project to work with
* Learn about and configure an OCI Identity Compartment
* Learn about and configure an OCI Object Storage Bucket
* Move our Terraform State file into an OCI Object Storage Bucket using HTTP backend
* Bonus: Add an account-wide Budget + Alert

## Foreword

This post was written in a home operations mindset, and in no-way envisions a fully-redundant cloud environment. For me personally, this is just a lab environment to play around with some ideas for home projects and learning purposes.

In order to get through this article, I recommend you already have...

* Basic understanding of cloud environments and their building blocks
* Basic understanding of Terraform
* Terraform installed locally and ready to go
* A credit card to attach to your OCI account
  * _Note: You can setup budgets in-case you're worried you might add services that cost money accidentally_

If you're still interested at this point, then let's jump in!

## Information and Sign-up

Before you can interact with the Oracle Cloud, you're going to need to create an account <span>&#128521;</span>

I highly recommend reading over the [Free Tier Overview](https://www.oracle.com/cloud/free/) at the very least. If that still leaves you with more questions about what the free tier provides, check out the more detailed [Free Tier Documentation](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier.htm).

## OCI Provider

The first thing we need to do before jumping into a new Terraform project, is understand how we configure the OCI Terraform provider.

### Usage

If we look at [the provider on the official Terraform Registry](https://registry.terraform.io/providers/oracle/oci/latest/docs), we find the basics to start a `main.tf` file with.

[![OCI Provider Usage Example](/img/posts/2023-07-25-oracle-cloud-free-tier/01.png)](https://registry.terraform.io/providers/oracle/oci/latest/docs)

### Configuration Options

Next-up, we need to understand what values the provider needs for configuration.

You can find the OCI Provider configuration options by going to Oracle's [Configuring the Provider](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/terraformproviderconfiguration.htm) page.

For this article, we're focused on the first (and default) authentication method, which is [API Key Authentication](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/terraformproviderconfiguration.htm#APIKeyAuth).

Follow the directions provided for each piece of required information.

{% capture clippy_note %}
<p>Since I'm just dipping my toes in, and not sure what I'm ultimately using OCI for yet, I went with creating my Public/Private key-pair through the OCI dashboard UI, downloaded the <code>.pem</code> private-key file, and used the <code>private_key_path</code> vs. <code>private_key</code> configuration option.</p>
<p>If you want to see some existing examples provided by Oracle, you can find them on the <a href="https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/terraformconfig.htm#terraformconfig_topic_Configuration_File_Requirements_Provider">Authoring Configurations</a> page.</p>
{% endcapture %}
{% include clippy-bubble.html content=clippy_note %}

In the end, you should have the following values ready:

| Config Variable  | Description | Documentation |
|-|-|-|
| `tenancy_ocid`     | OCID of your tenancy. | [Where to Get the Tenancy's OCID and User's OCID](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#five) |
| `user_ocid`        | OCID of the user calling the API. | [Where to Get the Tenancy's OCID and User's OCID](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#five) |
| `private_key_path` | The path (including filename) of the private key stored on your computer. | [How to Generate an API Signing Key](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#two) |
| `fingerprint`      | Fingerprint for the key pair being used. | [How to Get the Key's Fingerprint](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#four) |
| `region`           | Your OCI region. | [Regions and Availability Domains](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm) |

### Files to create

<div class="wrapper">
    <div class="tabs">
        <div class="tab">
            <input type="radio" name="css-tabs" id="tab-1" checked class="tab-switch">
            <label for="tab-1" class="tab-label">variables.tf</label>
            <div class="tab-content">
                <pre>
                    variable "tenancy_ocid" {
                        type        = string
                        description = "OCID of your tenancy."
                        default     = ""
                        sensitive = true
                    }

                    variable "user_ocid" {
                        type        = string
                        description = "OCID of the user calling the API."
                        default     = ""
                        sensitive = true
                    }

                    variable "fingerprint" {
                        type        = string
                        description = "Fingerprint for the key pair being used."
                        default     = ""
                        sensitive = true
                    }

                    variable "private_key_path" {
                        type        = string
                        description = "The path (including filename) of the private key stored on your computer."
                        default     = ""
                    }

                    variable "region" {
                        type        = string
                        description = "An OCI region."
                        default     = "us-ashburn-1"
                    }
                </pre>
            </div>
        </div>
        <div class="tab">
            <input type="radio" name="css-tabs" id="tab-2" class="tab-switch">
            <label for="tab-2" class="tab-label">main.tf</label>
            <div class="tab-content">
                ```hcl
                terraform {
                    required_providers {
                        oci = {
                        source = "oracle/oci"
                        version = "5.5.0"
                        }
                    }
                }

                provider "oci" {
                    tenancy_ocid = var.tenancy_ocid
                    user_ocid = var.user_ocid
                    fingerprint = var.fingerprint
                    private_key_path = var.private_key_path
                    region = var.region
                }
                ```
            </div>
        </div>
        <div class="tab">
            <input type="radio" name="css-tabs" id="tab-3" class="tab-switch">
            <label for="tab-3" class="tab-label">terraform.tfvars</label>
            <div class="tab-content">
                
            </div>
        </div>
    </div>
    <p>Example line outside of tab box</p>
</div>



You can now run `init`/`plan`/`apply` with Terraform, and that should leave you with a baseline state to work with going forward.

```shell
$ terraform init
$ terraform plan
$ terraform apply
```

## Creating your first resource

A good first place to start with your shiny new Oracle Cloud - Free Tier account, is provisioning a non-root Compartment resource.

If you're unsure what a compartment is, take a quick 2-minute read over at [Understanding Compartments](https://docs.oracle.com/en-us/iaas/Content/GSG/Concepts/settinguptenancy.htm#Understa).

The OCI provider resource that provisions compartments is named: [`oci_identity_compartment`](https://registry.terraform.io/providers/oracle/oci/latest/docs/resources/identity_compartment)

Before you add your first compartment resource, let's add a new variable in `variables.tf`, so that we can pass-in the OCID for a "parent" compartment _(in this case, the root compartment that was created when you made your OCI account)_.

variables.tf
```hcl
variable "root_compartment_ocid" {
  type        = string
  description = "The root_compartment_ocid to be used by child compartments."
  default     = ""
  sensitive = true
}
```

And now let's add the new compartment resource to the `main.tf` file...

main.tf
```hcl
resource "oci_identity_compartment" "home_ops" {
    compartment_id = var.root_compartment_ocid
    description = "Home Ops"
    name = "home-ops"
}
```

Now let's run `terraform plan` and see what things look like...

```shell
$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # oci_identity_compartment.home_ops will be created
  + resource "oci_identity_compartment" "home_ops" {
      + compartment_id = (sensitive value)
      + defined_tags   = (known after apply)
      + description    = "Home Ops"
      + freeform_tags  = (known after apply)
      + id             = (known after apply)
      + inactive_state = (known after apply)
      + is_accessible  = (known after apply)
      + name           = "home-ops"
      + state          = (known after apply)
      + time_created   = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

Sweet! Let's apply these changes and check things in the OCI Dashboard to verify it did what we expected...

```shell
$ terraform apply

...

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

oci_identity_compartment.home_ops: Creating...
oci_identity_compartment.home_ops: Creation complete after 8s [id=<some OCID value here>]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Checking in the OCI Dashboard...

If you now visit the compartments page at https://cloud.oracle.com/identity/compartments, you should see your new Compartment like-so:

![Compartment](/img/posts/2023-07-25-oracle-cloud-free-tier/02.png)

{% capture warning_note %}
<p><strong>If you make a mistake and need to remove an <code>oci_identity_compartment</code> resource, you also need to go into the OCI Dashboard and delete the resource explicitly.<br/>This is because <a href="https://registry.terraform.io/providers/oracle/oci/latest/docs/resources/identity_compartment">the OCI Provider for Terraform uses a hands-off approach when deleting Compartments</a>.</strong></p>
{% endcapture %}
{% include warning-bubble.html content=warning_note %}

## Don't fudge with ManagedCompartmentForPaaS

You may see a Compartment named `ManagedCompartmentForPaaS` appear randomly over-time. This is an account that the Oracle Cloud creates on its own, and how it allows certain Services in the cloud to access your personal resources.

See [Resources Created in Your Tenancy by Oracle](https://docs.oracle.com/en-us/iaas/Content/General/Reference/PaaSprereqs.htm#resources) for more information on this.

## Provisioning a Storage Bucket

The last thing we'll do in this article, is take a look at provisioning a Storage Bucket and moving our Terraform State up into the cloud itself.

The OCI provider resource to use when configuring storage buckets is named: [`oci_objectstorage_bucket`](https://registry.terraform.io/providers/oracle/oci/latest/docs/resources/objectstorage_bucket)

```hcl
resource "oci_objectstorage_bucket" "home_ops_terraform_state" {
    compartment_id = oci_identity_compartment.home_ops.id
    namespace = var.object_storage_namespace
    name = "terraform-state"
}
```

```shell
$ terraform plan

oci_identity_compartment.home_ops: Refreshing state... [id=<some ocid value>]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # oci_objectstorage_bucket.home_ops_terraform_state will be created
  + resource "oci_objectstorage_bucket" "home_ops_terraform_state" {
      + access_type                  = "NoPublicAccess"
      + approximate_count            = (known after apply)
      + approximate_size             = (known after apply)
      + auto_tiering                 = (known after apply)
      + bucket_id                    = (known after apply)
      + compartment_id               = "<some ocid value>"
      + created_by                   = (known after apply)
      + defined_tags                 = (known after apply)
      + etag                         = (known after apply)
      + freeform_tags                = (known after apply)
      + id                           = (known after apply)
      + is_read_only                 = (known after apply)
      + kms_key_id                   = (known after apply)
      + name                         = "terraform-state"
      + namespace                    = (sensitive value)
      + object_events_enabled        = (known after apply)
      + object_lifecycle_policy_etag = (known after apply)
      + replication_enabled          = (known after apply)
      + storage_tier                 = (known after apply)
      + time_created                 = (known after apply)
      + versioning                   = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

```shell
$ terraform apply

...

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

oci_objectstorage_bucket.home_ops_terraform_state: Creating...
oci_objectstorage_bucket.home_ops_terraform_state: Creation complete after 1s [id=n/<storage namespace>/b/terraform-state]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

![Storage Bucket](/img/posts/2023-07-25-oracle-cloud-free-tier/03.png)

## Adding an HTTP-based storage Backend

```hcl
backend "http" {
  update_method = "PUT"
}
```

```shell
$ export TF_HTTP_ADDRESS=https://abcdefghijkl.objectstorage.us-ashburn-1.oci.customer-oci.com/p/abcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyz/n/abcdefghijkl/b/home-ops/o/terraform.tfstate

$ terraform init
```

## Bonus: Deleting Compartments with logs

If you make a mistake, change your mind, etc. re: a Compartment you've created; attempting to delete it may result in an Error or Failure state saying it couldn't be removed. This is most likely due to the fact that you have a Log Group and Logs attached to the Compartment at this point.

If you attempt to go delete the Log and Log Groups directly, you'll find the OCI UI doesn't allow you to do this _(pretty stupid and aggravating)_.

The easiest way to get around this is to open up the Deverloper Tools -> Cloud Shell in the OCI Web UI and use the `oci` CLI binary to remove them manually.

You'll want to focus on the command sub-groups [`oci logging log`](https://docs.oracle.com/en-us/iaas/tools/oci-cli/3.30.1/oci_cli_docs/cmdref/logging/log.html) and [`oci logging log-group`](https://docs.oracle.com/en-us/iaas/tools/oci-cli/3.30.1/oci_cli_docs/cmdref/logging/log-group.html).

## Bonus: Add an account-wide budget + alert

You can add bugets at a Compartment or Cost-Tracking Tag scope. Since I haven't been tagging anything, I'm going to just focus on a single Budget that covers the entire Root Compartment scope.

To see how Budgets work, you can check out the [Budgets Overview](https://docs.oracle.com/en-us/iaas/Content/Billing/Concepts/budgetsoverview.htm) page.

```hcl
resource "oci_budget_budget" "root_compartment_budget" {
    amount = 10
    compartment_id = var.root_compartment_ocid
    reset_period = "MONTHLY"
    description = "Account-wide budget"
    display_name = "Account-wide"
    target_type = "COMPARTMENT"
    targets = [ var.root_compartment_ocid ]
}
```

```hcl
resource "oci_budget_alert_rule" "root_compartment_budget_alert_rule" {
    budget_id = oci_budget_budget.root_compartment_budget.id
    type = "FORECAST"
    message = "!! OCI Budget Alert - 80% Forecast Triggered !!"
    recipients = var.budget_alert_email_recipient
    threshold = 80
    threshold_type = "PERCENTAGE"
    display_name = "80-percent-budget-forecast"
    description = "Alerts when the Budget Forecast hits 80% or higher."
}
```

```shell
$ terraform plan

oci_identity_compartment.home_ops: Refreshing state... [id=<some OCID value>]
oci_objectstorage_bucket.home_ops_terraform_state: Refreshing state... [id=n/id9oxdiaaper/b/terraform-state]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # oci_budget_alert_rule.root_compartment_budget_alert_rule will be created
  + resource "oci_budget_alert_rule" "root_compartment_budget_alert_rule" {
      + budget_id      = (known after apply)
      + defined_tags   = (known after apply)
      + description    = "Alerts when the Budget Forecast hits 80% or higher."
      + display_name   = "80-percent-budget-forecast"
      + freeform_tags  = (known after apply)
      + id             = (known after apply)
      + message        = "!! OCI Budget Alert - 80% Forecast Triggered !!"
      + recipients     = (sensitive value)
      + state          = (known after apply)
      + threshold      = 80
      + threshold_type = "PERCENT"
      + time_created   = (known after apply)
      + time_updated   = (known after apply)
      + type           = "FORECAST"
      + version        = (known after apply)
    }

  # oci_budget_budget.root_compartment_budget will be created
  + resource "oci_budget_budget" "root_compartment_budget" {
      + actual_spend                          = (known after apply)
      + alert_rule_count                      = (known after apply)
      + amount                                = 10
      + budget_processing_period_start_offset = (known after apply)
      + compartment_id                        = (sensitive value)
      + defined_tags                          = (known after apply)
      + description                           = "Account-wide budget"
      + display_name                          = "Account-wide"
      + forecasted_spend                      = (known after apply)
      + freeform_tags                         = (known after apply)
      + id                                    = (known after apply)
      + processing_period_type                = (known after apply)
      + reset_period                          = "MONTHLY"
      + state                                 = (known after apply)
      + target_compartment_id                 = (known after apply)
      + target_type                           = "COMPARTMENT"
      + targets                               = [
          + (sensitive value),
        ]
      + time_created                          = (known after apply)
      + time_spend_computed                   = (known after apply)
      + time_updated                          = (known after apply)
      + version                               = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```


```shell
$ terraform apply

...

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

oci_budget_budget.root_compartment_budget: Creating...
oci_budget_budget.root_compartment_budget: Creation complete after 0s [id=<some OCID value>]
oci_budget_alert_rule.root_compartment_budget_alert_rule: Creating...
oci_budget_alert_rule.root_compartment_budget_alert_rule: Creation complete after 1s [id=<some OCID value>]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

![Billing & Cost Management -> Budgets](/img/posts/2023-07-25-oracle-cloud-free-tier/menu-budgets.png)

![Account-wide Budget](/img/posts/2023-07-25-oracle-cloud-free-tier/account-wide-budget.png)

![Account-wide Budget - Detailed](/img/posts/2023-07-25-oracle-cloud-free-tier/account-wide-budget-detailed.png)

## Other: Using an S3-based Backend

If the HTTP Backend isn't your cup-of-tea and/or you need to use S3 for other reasons regardless; here's an 
example of using the S3 Backend instead...

```hcl
backend "s3" {
    bucket   = "terraform-state"
    key      = "free-account/terraform.tfstate"
    region   = "us-ashburn-1"
    shared_credentials_file     = "./terraform-bucket-credentials"
    skip_region_validation      = true
    skip_credentials_validation = true
    skip_metadata_api_check     = true
    force_path_style            = true
}
```

{% capture warning_note %}
<p><strong>Make sure you don't commit your <code>terraform-bucket-credentials</code> file. Add it to <code>.gitignore</code> or save it else-where (like offline storage or a Key Vault)</strong></p>
{% endcapture %}
{% include warning-bubble.html content=warning_note %}

```shell
$ export AWS_ACCESS_KEY_ID=abcdefghijklmnopqrstuvwxyzabcdefghijklmn
$ export AWS_SECRET_ACCESS_KEY=abcdefghijklmnopqrstuvwxyzabcdefghijklmnopqr
$ export AWS_S3_ENDPOINT=https://abcdefghijkl.compat.objectstorage.us-ashburn-1.oraclecloud.com

$ terraform init

Initializing the backend...
Do you want to copy existing state to the new backend?
  Pre-existing state was found while migrating the previous "local" backend to the
  newly configured "s3" backend. No existing state was found in the newly
  configured "s3" backend. Do you want to copy this state to the new "s3"
  backend? Enter "yes" to copy and "no" to start with an empty state.

  Enter a value: yes


Successfully configured the backend "s3"! Terraform will automatically
use this backend unless the backend configuration changes.

Initializing provider plugins...
- Reusing previous version of oracle/oci from the dependency lock file
- Using previously-installed oracle/oci v5.5.0

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

![Terraform State in Object Storage](/img/posts/2023-07-25-oracle-cloud-free-tier/04.png)

## Closing

I hope this article was helpful for those looking to dip their toes in the water with Oracle Cloud Infrastructure and/or some basic Terraform usage.

I look forward to trying out some ARM CPU related loads and doing more with their service offerings in the future.
