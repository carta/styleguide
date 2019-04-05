# Writing Terraform Code

For any style question not answered here, please refer to:
* https://github.com/bsnape/terraform-style-guide

## Folder Structure

The top level of a repostory has two folders. `live` and `modules`.

```
live             # Contains the configurations for each AWS account
|_ data_science  # All the configurations of modules used by the data_science AWS account.
|_ management    # All the configurations of modules used by the management AWS account.
|_ production    # All the configurations of modules used by the production AWS account.
modules
|_ route53       # modules that are used by the "live" folders above.
```

## Backends
For Terraform Enterprise (or other specialized backends), ensure that you have a `backend.tf` that points to the location where the `terraform` command (run locally) should pull existing state from.

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

# Contributing
Feel free to open tickets or send pull requests with improvements. Thanks in
advance for your help!

## How to Contribute?

It's easy:

* [Fork](https://help.github.com/articles/fork-a-repo) the project on GitHub.
* Make your feature addition or bug fix in a feature branch. (Include a description of your changes)
* Push your feature branch to GitHub.
* Send a [Pull Request](https://help.github.com/articles/using-pull-requests).

# License

![Creative Commons License](https://licensebuttons.net/l/by/4.0/88x31.png)
This work is licensed under a [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/deed.en_US)
