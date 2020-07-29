# Usage
<!--- BEGIN_TF_DOCS --->
## Requirements

| Name | Version |
|------|---------|
| terraform | >= 0.12 |
| aws | >= 2.58.0 |

## Providers

| Name | Version |
|------|---------|
| aws | >= 2.58.0 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| enabled | Change to false to avoid deploying any AWS Backup resources | `bool` | `true` | no |
| plan\_name | The display name of a backup plan | `string` | n/a | yes |
| rule\_completion\_window | The amount of time AWS Backup attempts a backup before canceling the job and returning an error | `number` | `null` | no |
| rule\_copy\_action\_destination\_vault\_arn | An Amazon Resource Name (ARN) that uniquely identifies the destination backup vault for the copied backup. | `string` | `null` | no |
| rule\_copy\_action\_lifecycle | The lifecycle defines when a protected resource is copied over to a backup vault and when it expires. | `map` | `{}` | no |
| rule\_lifecycle\_cold\_storage\_after | Specifies the number of days after creation that a recovery point is moved to cold storage | `number` | `null` | no |
| rule\_lifecycle\_delete\_after | Specifies the number of days after creation that a recovery point is deleted. Must be 90 days greater than `cold_storage_after` | `number` | `null` | no |
| rule\_name | An display name for a backup rule | `string` | `null` | no |
| rule\_recovery\_point\_tags | Metadata that you can assign to help organize the resources that you create | `map(string)` | `{}` | no |
| rule\_schedule | A CRON expression specifying when AWS Backup initiates a backup job | `string` | `null` | no |
| rule\_start\_window | The amount of time in minutes before beginning a backup | `number` | `null` | no |
| rules | A list of rule maps | `any` | `[]` | no |
| selection\_name | The display name of a resource selection document | `string` | `null` | no |
| selection\_resources | An array of strings that either contain Amazon Resource Names (ARNs) or match patterns of resources to assign to a backup plan | `list` | `[]` | no |
| selection\_tag\_key | The key in a key-value pair | `string` | `null` | no |
| selection\_tag\_type | An operation, such as StringEquals, that is applied to a key-value pair used to filter resources in a selection | `string` | `null` | no |
| selection\_tag\_value | The value in a key-value pair | `string` | `null` | no |
| selections | A list of selction maps | `list` | `[]` | no |
| tags | A mapping of tags to assign to the resource | `map(string)` | `{}` | no |
| vault\_kms\_key\_arn | The server-side encryption key that is used to protect your backups | `string` | `null` | no |
| vault\_name | Name of the backup vault to create. If not given, AWS use default | `string` | `null` | no |

## Outputs

| Name | Description |
|------|-------------|
| plan\_arn | The ARN of the backup plan |
| plan\_id | The id of the backup plan |
| plan\_version | Unique, randomly generated, Unicode, UTF-8 encoded string that serves as the version ID of the backup plan |
| vault\_arn | The ARN of the vault |
| vault\_id | The name of the vault |

<!--- END_TF_DOCS --->
