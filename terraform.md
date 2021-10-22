# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM

Let us continue to our Infrastructure Automation with Terraform. Based on the knowledge from the [previous](https://github.com/brpo01/terraform-1/blob/master/terraform.md) project lets keep on creating AWS resources!!.

![tooling_project_15](https://user-images.githubusercontent.com/76074379/126079856-ac2b5dea-45d0-4f1f-85fa-54284a91a5de.png)

## Networking

Create 4 private subnets keeping in mind following principles:

- Make sure you use variables or length() function to determine the number of AZs

- Use variables and cidrsubnet() function to allocate vpc_cidr for subnets.

- Keep variables and resources in separate files for better code structure and readability
Tags all the resources you have created so far. 

- Explore how to use format() and count functions to automatically tag subnets with its respective number.

Tagging is a straightforward, but a very powerful concept that helps you manage your resources much more efficiently:

- Resources are much better organized in ‘virtual’ groups

- They can be easily filtered and searched from console or programmatically

- Billing team can easily generate reports and determine how much each part of infrastructure costs how much (by department, by type, by environment, etc.)

- You can easily determine resources that are not being used and take actions accordingly

- If there are different teams in the organisation using the same account, tagging can help differentiate who owns which resources.

**Note**: You can add multiple tags as a default set. for example, in out terraform.tfvars file we can have default tags defined.

```
tags = {
  Enviroment      = "production" 
  Owner-Email     = "opraise00@gmail.com"
  Managed-By      = "Terraform"
  Billing-Account = "1234567890"
}
```

Now you can tag all you resources using the format below:

```
tags = merge(
    var.tags,
    {
      Name = "Name of the resource"
    },
  )
```

**NOTE**: Update the variables.tf to declare the variable tags used in the format above:

```
variable "tags" {
  description = "A mapping of tags to assign to all resources."
  type        = map(string)
  default     = {}
}
```

