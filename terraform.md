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

**NOTE**: Update the *variables.tf* to declare the variable tags used in the format above:

```
variable "tags" {
  description = "A mapping of tags to assign to all resources."
  type        = map(string)
  default     = {}
}
```

## Internet Gateway Resource

- Create an Internet Gateway in a separate Terraform file internet_gateway.tf

```
resource "aws_internet_gateway" "ig" {
  vpc_id = aws_vpc.main.id

  tags = merge(
    var.tags,
    {
      Name = format("%s-%s!", aws_vpc.main.id,"IG")
    } 
  )
}
```

## NAT Gateway & Elastic IP Resource

- Create NAT Gateways and Elastic IP (EIP) resources based on the given reference architecture. Now use a similar approach to create the NAT Gateways in a new file called natgateway.tf.

```
resource "aws_eip" "nat_eip" {
  vpc        = true
  depends_on = [aws_internet_gateway.ig]

  tags = merge(
    var.tags,
    {
      Name = format("%s-EIP", var.name)
    },
  )
}

resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat_eip.id
  subnet_id     = element(aws_subnet.public.*.id, 0)
  depends_on    = [aws_internet_gateway.ig]

  tags = merge(
    var.tags,
    {
      Name = format("%s-Nat", var.name)
    },
  )
}
```
**Note**: We need to create an Elastic IP for the NAT Gateway, and you can see the use of depends_on to indicate that the Internet Gateway resource must be available before this should be created. Although Terraform does a good job to manage dependencies, but in some cases, it is good to be explicit.

## AWS Routes Resources