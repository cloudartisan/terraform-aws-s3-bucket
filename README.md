# terraform-aws-s3-bucket [![GitHub Action Tests](https://github.com/cloudposse/terraform-aws-s3-bucket/workflows/test/badge.svg?branch=master)](https://github.com/cloudposse/terraform-aws-s3-bucket/actions) [![Latest Release](https://img.shields.io/github/release/cloudposse/terraform-aws-s3-bucket.svg)](https://github.com/cloudposse/terraform-aws-s3-bucket/releases/latest) [![Slack Community](https://slack.cloudposse.com/badge.svg)](https://slack.cloudposse.com)

[![README Header][readme_header_img]][readme_header_link]

[![Cloud Posse][logo]](https://cpco.io/homepage)

<!--




  ** DO NOT EDIT THIS FILE
  **
  ** This file was automatically generated by the `build-harness`.
  ** 1) Make all changes to `README.yaml`
  ** 2) Run `make init` (you only need to do this once)
  ** 3) Run`make readme` to rebuild this file.
  **
  ** (We maintain HUNDREDS of open source projects. This is how we maintain our sanity.)
  **





-->

This module creates an S3 bucket with support of versioning, encryption, ACL and bucket object policy.
If `user_enabled` variable is set to `true`, the module will provision a basic IAM user with permissions to access the bucket.

This basic IAM system user is suitable for CI/CD systems (_e.g._ TravisCI, CircleCI) or systems which are *external* to AWS that cannot leverage [AWS IAM Instance Profiles](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html).

We do not recommend creating IAM users this way for any other purpose.

It blocks public access to the bucket by default.
https://docs.aws.amazon.com/AmazonS3/latest/dev/access-control-block-public-access.html


---

This project is part of our comprehensive ["SweetOps"](https://cpco.io/sweetops) approach towards DevOps.
[<img align="right" title="Share via Email" src="https://docs.cloudposse.com/images/ionicons/ios-email-outline-2.0.1-16x16-999999.svg"/>][share_email]
[<img align="right" title="Share on Google+" src="https://docs.cloudposse.com/images/ionicons/social-googleplus-outline-2.0.1-16x16-999999.svg" />][share_googleplus]
[<img align="right" title="Share on Facebook" src="https://docs.cloudposse.com/images/ionicons/social-facebook-outline-2.0.1-16x16-999999.svg" />][share_facebook]
[<img align="right" title="Share on Reddit" src="https://docs.cloudposse.com/images/ionicons/social-reddit-outline-2.0.1-16x16-999999.svg" />][share_reddit]
[<img align="right" title="Share on LinkedIn" src="https://docs.cloudposse.com/images/ionicons/social-linkedin-outline-2.0.1-16x16-999999.svg" />][share_linkedin]
[<img align="right" title="Share on Twitter" src="https://docs.cloudposse.com/images/ionicons/social-twitter-outline-2.0.1-16x16-999999.svg" />][share_twitter]


[![Terraform Open Source Modules](https://docs.cloudposse.com/images/terraform-open-source-modules.svg)][terraform_modules]



It's 100% Open Source and licensed under the [APACHE2](LICENSE).







We literally have [*hundreds of terraform modules*][terraform_modules] that are Open Source and well-maintained. Check them out!







## Usage


**IMPORTANT:** The `master` branch is used in `source` just as an example. In your code, do not pin to `master` because there may be breaking changes between releases.
Instead pin to the release tag (e.g. `?ref=tags/x.y.z`) of one of our [latest releases](https://github.com/cloudposse/terraform-aws-s3-bucket/releases).


Using a [canned ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html).

```hcl
module "s3_bucket" {
  source                   = "git::https://github.com/cloudposse/terraform-aws-s3-bucket.git?ref=master"
  acl                      = "private"
  enabled                  = true
  user_enabled             = true
  versioning_enabled       = false
  allowed_bucket_actions   = ["s3:GetObject", "s3:ListBucket", "s3:GetBucketLocation"]
  name                     = "app"
  stage                    = "test"
  namespace                = "eg"
}
```

Using [grants](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) to enable access to another account and for logging.

```hcl
module "s3_bucket" {
  source                   = "git::https://github.com/cloudposse/terraform-aws-s3-bucket.git?ref=master"
  acl                      = ""
  enabled                  = true
  user_enabled             = true
  versioning_enabled       = false
  allowed_bucket_actions   = ["s3:GetObject", "s3:ListBucket", "s3:GetBucketLocation"]
  name                     = "app"
  stage                    = "test"
  namespace                = "eg"

  grants = [
    {
      id          = "012abc345def678ghi901" # Canonical user or account id
      type        = "CanonicalUser"
      permissions = ["FULL_CONTROL"]
      uri         = null
    },
    {
      id          = null
      type        = "Group"
      permissions = ["READ", "WRITE"]
      uri         = "http://acs.amazonaws.com/groups/s3/LogDelivery"
    },
  ]
}
```






<!-- markdownlint-disable -->
## Makefile Targets
```text
Available targets:

  help                                Help screen
  help/all                            Display help for all targets
  help/short                          This help short screen
  lint                                Lint terraform code
  test/%                              Run Terraform commands in the examples/complete folder; e.g. make test/plan

```
<!-- markdownlint-restore -->
<!-- markdownlint-disable -->
## Requirements

| Name | Version |
|------|---------|
| terraform | >= 0.12.0 |
| aws | >= 3.0 |
| local | >= 1.2 |
| null | >= 2.0 |

## Providers

| Name | Version |
|------|---------|
| aws | >= 3.0 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| abort\_incomplete\_multipart\_upload\_days | Maximum time (in days) that you want to allow multipart uploads to remain in progress | `number` | `5` | no |
| acl | The [canned ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl) to apply. We recommend `private` to avoid exposing sensitive information. Conflicts with `grants`. | `string` | `"private"` | no |
| additional\_tag\_map | Additional tags for appending to tags\_as\_list\_of\_maps. Not added to `tags`. | `map(string)` | `{}` | no |
| allow\_encrypted\_uploads\_only | Set to `true` to prevent uploads of unencrypted objects to S3 bucket | `bool` | `false` | no |
| allowed\_bucket\_actions | List of actions the user is permitted to perform on the S3 bucket | `list(string)` | <pre>[<br>  "s3:PutObject",<br>  "s3:PutObjectAcl",<br>  "s3:GetObject",<br>  "s3:DeleteObject",<br>  "s3:ListBucket",<br>  "s3:ListBucketMultipartUploads",<br>  "s3:GetBucketLocation",<br>  "s3:AbortMultipartUpload"<br>]</pre> | no |
| attributes | Additional attributes (e.g. `1`) | `list(string)` | `[]` | no |
| block\_public\_acls | Set to `false` to disable the blocking of new public access lists on the bucket | `bool` | `true` | no |
| block\_public\_policy | Set to `false` to disable the blocking of new public policies on the bucket | `bool` | `true` | no |
| context | Single object for setting entire context at once.<br>See description of individual variables for details.<br>Leave string and numeric variables as `null` to use default value.<br>Individual variable settings (non-null) override settings in context object,<br>except for attributes, tags, and additional\_tag\_map, which are merged. | <pre>object({<br>    enabled             = bool<br>    namespace           = string<br>    environment         = string<br>    stage               = string<br>    name                = string<br>    delimiter           = string<br>    attributes          = list(string)<br>    tags                = map(string)<br>    additional_tag_map  = map(string)<br>    regex_replace_chars = string<br>    label_order         = list(string)<br>    id_length_limit     = number<br>  })</pre> | <pre>{<br>  "additional_tag_map": {},<br>  "attributes": [],<br>  "delimiter": null,<br>  "enabled": true,<br>  "environment": null,<br>  "id_length_limit": null,<br>  "label_order": [],<br>  "name": null,<br>  "namespace": null,<br>  "regex_replace_chars": null,<br>  "stage": null,<br>  "tags": {}<br>}</pre> | no |
| cors\_rule\_inputs | Specifies the allowed headers, methods, origins and exposed headers when using CORS on this bucket | <pre>list(object({<br>    allowed_headers = list(string)<br>    allowed_methods = list(string)<br>    allowed_origins = list(string)<br>    expose_headers  = list(string)<br>    max_age_seconds = number<br>  }))</pre> | `null` | no |
| delimiter | Delimiter to be used between `namespace`, `environment`, `stage`, `name` and `attributes`.<br>Defaults to `-` (hyphen). Set to `""` to use no delimiter at all. | `string` | `null` | no |
| enable\_glacier\_transition | Enables the transition to AWS Glacier which can cause unnecessary costs for huge amount of small files | `bool` | `true` | no |
| enable\_standard\_ia\_transition | Enables the transition to STANDARD\_IA | `bool` | `false` | no |
| enabled | Set to false to prevent the module from creating any resources | `bool` | `null` | no |
| environment | Environment, e.g. 'uw2', 'us-west-2', OR 'prod', 'staging', 'dev', 'UAT' | `string` | `null` | no |
| expiration\_days | Number of days after which to expunge the objects | `number` | `90` | no |
| force\_destroy | A boolean string that indicates all objects should be deleted from the bucket so that the bucket can be destroyed without error. These objects are not recoverable | `bool` | `false` | no |
| glacier\_transition\_days | Number of days after which to move the data to the glacier storage tier | `number` | `60` | no |
| grants | An ACL policy grant. Conflicts with `acl`. Set `acl` to `null` to use this. | <pre>list(object({<br>    id          = string<br>    type        = string<br>    permissions = list(string)<br>    uri         = string<br>  }))</pre> | `null` | no |
| id\_length\_limit | Limit `id` to this many characters.<br>Set to `0` for unlimited length.<br>Set to `null` for default, which is `0`.<br>Does not affect `id_full`. | `number` | `null` | no |
| ignore\_public\_acls | Set to `false` to disable the ignoring of public access lists on the bucket | `bool` | `true` | no |
| kms\_master\_key\_arn | The AWS KMS master key ARN used for the `SSE-KMS` encryption. This can only be used when you set the value of `sse_algorithm` as `aws:kms`. The default aws/s3 AWS KMS master key is used if this element is absent while the `sse_algorithm` is `aws:kms` | `string` | `""` | no |
| label\_order | The naming order of the id output and Name tag.<br>Defaults to ["namespace", "environment", "stage", "name", "attributes"].<br>You can omit any of the 5 elements, but at least one must be present. | `list(string)` | `null` | no |
| lifecycle\_rule\_enabled | Enable or disable lifecycle rule | `bool` | `false` | no |
| lifecycle\_tags | Tags filter. Used to manage object lifecycle events | `map(string)` | `{}` | no |
| name | Solution name, e.g. 'app' or 'jenkins' | `string` | `null` | no |
| namespace | Namespace, which could be your organization name or abbreviation, e.g. 'eg' or 'cp' | `string` | `null` | no |
| noncurrent\_version\_expiration\_days | Specifies when noncurrent object versions expire | `number` | `90` | no |
| noncurrent\_version\_transition\_days | Number of days to persist in the standard storage tier before moving to the glacier tier infrequent access tier | `number` | `30` | no |
| policy | A valid bucket policy JSON document. Note that if the policy document is not specific enough (but still valid), Terraform may view the policy as constantly changing in a terraform plan. In this case, please make sure you use the verbose/specific version of the policy | `string` | `""` | no |
| prefix | Prefix identifying one or more objects to which the rule applies | `string` | `""` | no |
| regex\_replace\_chars | Regex to replace chars with empty string in `namespace`, `environment`, `stage` and `name`.<br>If not set, `"/[^a-zA-Z0-9-]/"` is used to remove all characters other than hyphens, letters and digits. | `string` | `null` | no |
| restrict\_public\_buckets | Set to `false` to disable the restricting of making the bucket public | `bool` | `true` | no |
| sse\_algorithm | The server-side encryption algorithm to use. Valid values are `AES256` and `aws:kms` | `string` | `"AES256"` | no |
| stage | Stage, e.g. 'prod', 'staging', 'dev', OR 'source', 'build', 'test', 'deploy', 'release' | `string` | `null` | no |
| standard\_transition\_days | Number of days to persist in the standard storage tier before moving to the infrequent access tier | `number` | `30` | no |
| tags | Additional tags (e.g. `map('BusinessUnit','XYZ')` | `map(string)` | `{}` | no |
| user\_enabled | Set to `true` to create an IAM user with permission to access the bucket | `bool` | `false` | no |
| versioning\_enabled | A state of versioning. Versioning is a means of keeping multiple variants of an object in the same bucket | `bool` | `false` | no |

## Outputs

| Name | Description |
|------|-------------|
| access\_key\_id | The access key ID |
| bucket\_arn | Bucket ARN |
| bucket\_domain\_name | FQDN of bucket |
| bucket\_id | Bucket Name (aka ID) |
| bucket\_region | Bucket region |
| bucket\_regional\_domain\_name | The bucket region-specific domain name |
| enabled | Is module enabled |
| secret\_access\_key | The secret access key. This will be written to the state file in plain-text |
| user\_arn | The ARN assigned by AWS for the user |
| user\_enabled | Is user creation enabled |
| user\_name | Normalized IAM user name |
| user\_unique\_id | The user unique ID assigned by AWS |

<!-- markdownlint-restore -->



## Share the Love

Like this project? Please give it a ★ on [our GitHub](https://github.com/cloudposse/terraform-aws-s3-bucket)! (it helps us **a lot**)

Are you using this project or any of our other projects? Consider [leaving a testimonial][testimonial]. =)


## Related Projects

Check out these related projects.

- [terraform-aws-cloudfront-s3-cdn](https://github.com/cloudposse/terraform-aws-cloudfront-s3-cdn) - Terraform module to easily provision CloudFront CDN backed by an S3 origin
- [terraform-aws-s3-website](https://github.com/cloudposse/terraform-aws-s3-website) - Terraform Module for Creating S3 backed Websites and Route53 DNS
- [terraform-aws-user-data-s3-backend](https://github.com/cloudposse/terraform-aws-user-data-s3-backend) - Terraform Module to Offload User Data to S3
- [terraform-aws-s3-logs-athena-query](https://github.com/cloudposse/terraform-aws-s3-logs-athena-query) - A Terraform module that creates an Athena Database and Structure for querying S3 access logs
- [terraform-aws-lb-s3-bucket](https://github.com/cloudposse/terraform-aws-lb-s3-bucket) - Terraform module to provision an S3 bucket with built in IAM policy to allow AWS Load Balancers to ship access logs
- [terraform-aws-s3-log-storage](https://github.com/cloudposse/terraform-aws-s3-log-storage) - Terraform module creates an S3 bucket suitable for receiving logs from other AWS services such as S3, CloudFront, and CloudTrail



## Help

**Got a question?** We got answers.

File a GitHub [issue](https://github.com/cloudposse/terraform-aws-s3-bucket/issues), send us an [email][email] or join our [Slack Community][slack].

[![README Commercial Support][readme_commercial_support_img]][readme_commercial_support_link]

## DevOps Accelerator for Startups


We are a [**DevOps Accelerator**][commercial_support]. We'll help you build your cloud infrastructure from the ground up so you can own it. Then we'll show you how to operate it and stick around for as long as you need us.

[![Learn More](https://img.shields.io/badge/learn%20more-success.svg?style=for-the-badge)][commercial_support]

Work directly with our team of DevOps experts via email, slack, and video conferencing.

We deliver 10x the value for a fraction of the cost of a full-time engineer. Our track record is not even funny. If you want things done right and you need it done FAST, then we're your best bet.

- **Reference Architecture.** You'll get everything you need from the ground up built using 100% infrastructure as code.
- **Release Engineering.** You'll have end-to-end CI/CD with unlimited staging environments.
- **Site Reliability Engineering.** You'll have total visibility into your apps and microservices.
- **Security Baseline.** You'll have built-in governance with accountability and audit logs for all changes.
- **GitOps.** You'll be able to operate your infrastructure via Pull Requests.
- **Training.** You'll receive hands-on training so your team can operate what we build.
- **Questions.** You'll have a direct line of communication between our teams via a Shared Slack channel.
- **Troubleshooting.** You'll get help to triage when things aren't working.
- **Code Reviews.** You'll receive constructive feedback on Pull Requests.
- **Bug Fixes.** We'll rapidly work with you to fix any bugs in our projects.

## Slack Community

Join our [Open Source Community][slack] on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

## Discourse Forums

Participate in our [Discourse Forums][discourse]. Here you'll find answers to commonly asked questions. Most questions will be related to the enormous number of projects we support on our GitHub. Come here to collaborate on answers, find solutions, and get ideas about the products and services we value. It only takes a minute to get started! Just sign in with SSO using your GitHub account.

## Newsletter

Sign up for [our newsletter][newsletter] that covers everything on our technology radar.  Receive updates on what we're up to on GitHub as well as awesome new projects we discover.

## Office Hours

[Join us every Wednesday via Zoom][office_hours] for our weekly "Lunch & Learn" sessions. It's **FREE** for everyone!

[![zoom](https://img.cloudposse.com/fit-in/200x200/https://cloudposse.com/wp-content/uploads/2019/08/Powered-by-Zoom.png")][office_hours]

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/cloudposse/terraform-aws-s3-bucket/issues) to report any bugs or file feature requests.

### Developing

If you are interested in being a contributor and want to get involved in developing this project or [help out](https://cpco.io/help-out) with our other projects, we would love to hear from you! Shoot us an [email][email].

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!


## Copyright

Copyright © 2017-2020 [Cloud Posse, LLC](https://cpco.io/copyright)



## License

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

See [LICENSE](LICENSE) for full details.

```text
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
```









## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

This project is maintained and funded by [Cloud Posse, LLC][website]. Like it? Please let us know by [leaving a testimonial][testimonial]!

[![Cloud Posse][logo]][website]

We're a [DevOps Professional Services][hire] company based in Los Angeles, CA. We ❤️  [Open Source Software][we_love_open_source].

We offer [paid support][commercial_support] on all of our projects.

Check out [our other projects][github], [follow us on twitter][twitter], [apply for a job][jobs], or [hire us][hire] to help with your cloud strategy and implementation.



### Contributors

|  [![Erik Osterman][osterman_avatar]][osterman_homepage]<br/>[Erik Osterman][osterman_homepage] | [![Andriy Knysh][aknysh_avatar]][aknysh_homepage]<br/>[Andriy Knysh][aknysh_homepage] | [![Maxim Mironenko][maximmi_avatar]][maximmi_homepage]<br/>[Maxim Mironenko][maximmi_homepage] | [![Josh Myers][joshmyers_avatar]][joshmyers_homepage]<br/>[Josh Myers][joshmyers_homepage] |
|---|---|---|---|

  [osterman_homepage]: https://github.com/osterman
  [osterman_avatar]: https://img.cloudposse.com/150x150/https://github.com/osterman.png
  [aknysh_homepage]: https://github.com/aknysh
  [aknysh_avatar]: https://img.cloudposse.com/150x150/https://github.com/aknysh.png
  [maximmi_homepage]: https://github.com/maximmi
  [maximmi_avatar]: https://img.cloudposse.com/150x150/https://github.com/maximmi.png
  [joshmyers_homepage]: https://github.com/joshmyers
  [joshmyers_avatar]: https://img.cloudposse.com/150x150/https://github.com/joshmyers.png

[![README Footer][readme_footer_img]][readme_footer_link]
[![Beacon][beacon]][website]

  [logo]: https://cloudposse.com/logo-300x69.svg
  [docs]: https://cpco.io/docs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=docs
  [website]: https://cpco.io/homepage?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=website
  [github]: https://cpco.io/github?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=github
  [jobs]: https://cpco.io/jobs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=jobs
  [hire]: https://cpco.io/hire?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=hire
  [slack]: https://cpco.io/slack?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=slack
  [linkedin]: https://cpco.io/linkedin?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=linkedin
  [twitter]: https://cpco.io/twitter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=twitter
  [testimonial]: https://cpco.io/leave-testimonial?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=testimonial
  [office_hours]: https://cloudposse.com/office-hours?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=office_hours
  [newsletter]: https://cpco.io/newsletter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=newsletter
  [discourse]: https://ask.sweetops.com/?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=discourse
  [email]: https://cpco.io/email?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=email
  [commercial_support]: https://cpco.io/commercial-support?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=commercial_support
  [we_love_open_source]: https://cpco.io/we-love-open-source?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=we_love_open_source
  [terraform_modules]: https://cpco.io/terraform-modules?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=terraform_modules
  [readme_header_img]: https://cloudposse.com/readme/header/img
  [readme_header_link]: https://cloudposse.com/readme/header/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=readme_header_link
  [readme_footer_img]: https://cloudposse.com/readme/footer/img
  [readme_footer_link]: https://cloudposse.com/readme/footer/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=readme_footer_link
  [readme_commercial_support_img]: https://cloudposse.com/readme/commercial-support/img
  [readme_commercial_support_link]: https://cloudposse.com/readme/commercial-support/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-s3-bucket&utm_content=readme_commercial_support_link
  [share_twitter]: https://twitter.com/intent/tweet/?text=terraform-aws-s3-bucket&url=https://github.com/cloudposse/terraform-aws-s3-bucket
  [share_linkedin]: https://www.linkedin.com/shareArticle?mini=true&title=terraform-aws-s3-bucket&url=https://github.com/cloudposse/terraform-aws-s3-bucket
  [share_reddit]: https://reddit.com/submit/?url=https://github.com/cloudposse/terraform-aws-s3-bucket
  [share_facebook]: https://facebook.com/sharer/sharer.php?u=https://github.com/cloudposse/terraform-aws-s3-bucket
  [share_googleplus]: https://plus.google.com/share?url=https://github.com/cloudposse/terraform-aws-s3-bucket
  [share_email]: mailto:?subject=terraform-aws-s3-bucket&body=https://github.com/cloudposse/terraform-aws-s3-bucket
  [beacon]: https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/terraform-aws-s3-bucket?pixel&cs=github&cm=readme&an=terraform-aws-s3-bucket
