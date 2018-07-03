# Writing Terraform Code

For any style question not answered here, please refer to:
* https://github.com/bsnape/terraform-style-guide

## Formatters
Before pushing any TF code, either run `terraform fmt` on the files you've changed, or configure your editor to automatically do so.

## Variables
When setting variables, preferences of how to do so is the following:
1) Data lookups
2) Default values

Whenever possible, favor `_` in place of `-` for a better IDE experience

## Modules
In order to keep DRY, use modules whenever possible

## Remote State
If necessary, you may fetch remote state; however, data lookups are preferrable to this whenever possible.
