![Terraform](https://lgallardo.com/images/terraform.jpg)
# terraform-aws-backup

Terraform module to create [AWS Backup](https://aws.amazon.com/backup/) plans.  AWS Backup is a fully managed backup service that makes it easy to centralize and automate the back up of data across AWS services (EBS volumes, RDS databases, DynamoDB tables, EFS file systems, and Storage Gateway volumes).

## Usage

You can use this module to create a simple plan using the module's `rule_*` variables. You can also  use the `rules` and `selections` list of maps variables to build a more complete plan by defining several rules and selections at once.

Check the [examples](examples/) for the  **simple plan**, the **simple plan with list** and the **complete plan** snippets.

### Example (complete plan)

This example creates a plan with two rules and two selections at once. It also defines a vault key which is used by the first rule because no `target_vault_name` was given (null). Whereas the second rule is using the "Default" vault key.

The first selection has two assignments, the first defined by a resource ARN and the second one defined by a tag condition. The second selection has just one assignment defined by a resource ARN.

```
module "aws_backup_example" {

  source = "../modules/terraform-aws-backup"

  # Vault
  vault_name = "vault-3"

  # Plan
  plan_name = "complete-plan"

  # Multiple rules using a list of maps
  rules = [
    {
      name              = "rule-1"
      schedule          = "cron(0 12 * * ? *)"
      target_vault_name = null
      start_window      = 120
      completion_window = 360
      lifecycle = {
        cold_storage_after = 0
        delete_after       = 90
      },
      copy_action = {
        lifecycle = {
          cold_storage_after = 0
          delete_after       = 90
        },
        destination_vault_arn = "arn:aws:backup:us-west-2:123456789101:backup-vault:Default"
      }
      recovery_point_tags = {
        Environment = "production"
      }
    },
    {
      name                = "rule-2"
      target_vault_name   = "Default"
      schedule            = null
      start_window        = 120
      completion_window   = 360
      lifecycle           = {}
      copy_action         = {}
      recovery_point_tags = {}
    },
  ]

  # Multiple selections
  #  - Selection-1: By resources and tag
  #  - Selection-2: Only by resources
  selections = [
    {
      name      = "selection-1"
      resources = ["arn:aws:dynamodb:us-east-1:123456789101:table/mydynamodb-table1"]
      selection_tag = {
        type  = "STRINGEQUALS"
        key   = "Environment"
        value = "production"
      }
    },
    {
      name          = "selection-2"
      resources     = ["arn:aws:dynamodb:us-east-1:123456789101:table/mydynamodb-table2"]
      selection_tag = {}
    },
  ]

  tags = {
    Owner       = "backup team"
    Environment = "production"
    Terraform   = true
  }
}
```

## Providers

| Name | Version |
|------|---------|
| aws | >= 2.58.0 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| enabled | Change to false to avoid deploying any AWS Backup resources | `bool` | `true` | no |
| plan\_name | The display name of a backup plan | `string` | n/a | yes |
| rule\_completion\_window | The amount of time AWS Backup attempts a backup before canceling the job and returning an error | `number` | n/a | yes |
| rule\_copy\_action\_destination\_vault\_arn | An Amazon Resource Name (ARN) that uniquely identifies the destination backup vault for the copied backup. | `string` | n/a | yes |
| rule\_copy\_action\_lifecycle | The lifecycle defines when a protected resource is copied over to a backup vault and when it expires. | `map` | `{}` | no |
| rule\_lifecycle\_cold\_storage\_after | Specifies the number of days after creation that a recovery point is moved to cold storage | `number` | n/a | yes |
| rule\_lifecycle\_delete\_after | Specifies the number of days after creation that a recovery point is deleted. Must be 90 days greater than `cold_storage_after` | `number` | n/a | yes |
| rule\_name | An display name for a backup rule | `string` | n/a | yes |
| rule\_recovery\_point\_tags | Metadata that you can assign to help organize the resources that you create | `map(string)` | `{}` | no |
| rule\_schedule | A CRON expression specifying when AWS Backup initiates a backup job | `string` | n/a | yes |
| rule\_start\_window | The amount of time in minutes before beginning a backup | `number` | n/a | yes |
| rules | A list of rule maps | `any` | `[]` | no |
| selection\_name | The display name of a resource selection document | `string` | n/a | yes |
| selection\_resources | An array of strings that either contain Amazon Resource Names (ARNs) or match patterns of resources to assign to a backup plan | `list` | `[]` | no |
| selection\_tag\_key | The key in a key-value pair | `string` | n/a | yes |
| selection\_tag\_type | An operation, such as StringEquals, that is applied to a key-value pair used to filter resources in a selection | `string` | n/a | yes |
| selection\_tag\_value | The value in a key-value pair | `string` | n/a | yes |
| selections | A list of selction maps | `list` | `[]` | no |
| tags | A mapping of tags to assign to the resource | `map(string)` | `{}` | no |
| vault\_kms\_key\_arn | The server-side encryption key that is used to protect your backups | `string` | n/a | yes |
| vault\_name | Name of the backup vault to create. If not given, AWS use default | `string` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| plan\_arn | The ARN of the backup plan |
| plan\_id | The id of the backup plan |
| plan\_version | Unique, randomly generated, Unicode, UTF-8 encoded string that serves as the version ID of the backup plan |
| vault\_arn | The ARN of the vault |
| vault\_id | The name of the vault |
