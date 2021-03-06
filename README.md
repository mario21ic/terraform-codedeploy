# terraform-codedeploy
Terraform modules that are related to codedeploy


## app
Create a codedeploy app

### Available variables
 * [`name`]: String(required): Name of your codedeploy application
 * [`project`]: String(required): The current project
 * [`s3_bucket_arn`]: String(optional): ARN of the S3 bucket where to fetch the application revision packages

### Output
* [`app_name`]: String: Name of the codedeploy app
* [`deployer_policy_id`]: String: IAM policy id for the user / role that will upload application revisions and trigger deployments
* [`deployer_policy_arn`]: String: IAM policy ARN for the user / role that will upload application revisions and trigger deployments
* [`deployer_policy_name`]: String: IAM policy name for the user / role that will upload application revisions and trigger deployments

### Example
```
  module "codedeploy" {
    source  = "github.com/skyscrapers/terraform-codedeploy//app"
    name    = "application"
    project = "example"
  }
```

## deployment_group
Create an deployment group for a codedeploy app

### Available variables
 * [`environment`]: String(required): Environment where your codedeploy deployment group is used for
 * [`app_name`]: String(required): Name of the coddeploy app
 * [`service_role_arn`]: String(required): IAM role that is used by the deployment group. You can use the [terraform-iam](https://github.com/skyscrapers/terraform-iam/blob/master/README.md#codedeploy_role) module for this.
 * [`autoscaling_groups`]: List(required): Autoscaling groups you want to attach to the deployment group


### Output
/

### Example
```
  module "deployment_group" {
    source             = "github.com/skyscrapers/terraform-codedeploy//deployment-group"
    environment        = "production"
    app_name           = "${module.codedeploy.app_name}"
    service_role_arn   = "${module.iam.arn_role}"
    autoscaling_groups = ["autoscaling1", "autoscaling2"]
  }
```

## deployment-group-ec2tag
Create an deployment group for a codedeploy app. This module will filter for tags

### Available variables
 * [`environment`]: String(required): Environment where your codedeploy deployment group is used for
 * [`app_name`]: String(required): Name of the coddeploy app
 * [`service_role_arn`]: String(required): IAM role that is used by the deployment group. You can use the [terraform-iam](https://github.com/skyscrapers/terraform-iam/blob/master/README.md#codedeploy_role) module for this.
 * [`filterkey`]: String(required):  The key of the tag you assigned to the instances belonging to this deployment group
 * [`filtervalue`]: String(required): The value of the tag you assigned to the instances belonging to this deployment group


### Output
/

### Example
```
  module "deployment_group-ec2tag" {
    source             = "github.com/skyscrapers/terraform-codedeploy//deployment-group-ec2tag"
    environment        = "production"
    app_name           = "${module.codedeploy.app_name}"
    service_role_arn   = "${module.iam.arn_role}"
    filterkey          = "app"
    filtervalue        = "web"
  }
```

## deployment-group-ec2tag-notify
Create an deployment group for a codedeploy app and will set the notifications. This module will filter for tags

### Available variables
 * [`environment`]: String(required): Environment where your codedeploy deployment group is used for
 * [`app_name`]: String(required): Name of the coddeploy app
 * [`service_role_arn`]: String(required): IAM role that is used by the deployment group. You can use the [terraform-iam](https://github.com/skyscrapers/terraform-iam/blob/master/README.md#codedeploy_role) module for this.
 * [`filterkey`]: String(required):  The key of the tag you assigned to the instances belonging to this deployment group
 * [`filtervalue`]: String(required): The value of the tag you assigned to the instances belonging to this deployment group
 * [`trigger_arn`]: String(required): SNS topic through which notifications are sent.
 * [`trigger_name`]: String(required): the name of the notification trigger.
 * [`trigger_events`]: String(optional): events that can trigger the notifications.

### Output
/

### Example
```
  module "deployment_group-ec2tag-notify" {
    source             = "github.com/skyscrapers/terraform-codedeploy//deployment-group-ec2tag-triggered"
    environment        = "production"
    app_name           = "${module.codedeploy.app_name}"
    service_role_arn   = "${module.iam.arn_role}"
    filterkey          = "app"
    filtervalue        = "web"
    trigger_name     = "SNSToSlack"
    trigger_arn      = "arn:aws:sns:eu-west-1:123456780:CodeDeploy"
  }
```

# S3 bucket

Create an S3 bucket to use with Codedeploy, to store application revisions.

See [s3bucket/variables.tf](s3bucket/variables.tf) for available variables.

### Outputs

* [`bucket_id`]: String: S3 bucket id
* [`bucket_arn`]: String: S3 bucket ARN
* [`policy_id`]: String: IAM policy id for the EC2 instances working with Codedeploy
* [`policy_arn`]: String: IAM policy ARN for the EC2 instances working with Codedeploy
* [`policy_name`]: String: IAM policy name for the EC2 instances working with Codedeploy

### Example

```
module "codedeploy_bucket" {
  source      = "github.com/skyscrapers/terraform-codedeploy//s3bucket?ref=478373f6f8d4a46b7a1ec96090707365e0ae3e42"
  name_prefix = "halito"
}
```
