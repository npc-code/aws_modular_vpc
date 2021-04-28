# aws_modular_vpc

terraform module to create an aws vpc, along with multiple private/public subnets, an Internet Gateway, multiple NAT gateways, and route tables.

## Requirements
- terraform
- aws account
- aws api credentials within a profile under ~/.aws/credentials or ~/.aws/config  

## Usage
This example will create a vpc with:
- base_cidr of 10.0.0.0/16
- 1 private and 1 public subnet per az.  since the subnet mask is /24, the subnets will be defined as 10.0.x.0/24, with x being a count index
- 1 internet gateway
- 1 nat gateway, along with an associated Elastic IP
- route tables for each private subnet, directing traffic bound for outside the vpc to the nat gateway
- route tables for the public subnets, directing traffic bound for outside the vpc through the internet gateway

```
provider "aws" {
  profile = var.profile
  region  = var.region
}

module "network" {
  source = "github.com/npc-code/aws_modular_vpc?ref=v1.0.0"
  az_count          = 2
  base_network      = "10.0.0.0"
  network_mask      = 16
  subnet_mask       = 24
  nat_gw_production = false
}

```


## Variables
- az_count
    - type = number
    - default = 0
    - number of azs to use.  if not set, all azs within the target region will be used for this deployment.

- base_network
    - type = string
    - default = "10.0.0.0"
    - base network ip to use.

- nat_gw_production
    - type = bool
    - default = true
    - set to true to follow best practice and deploy a nat gateway in every az for use by the private subnet.  false will create only one. 

- network_mask
    - type = number
    - default = 16
    - used to define the network address portion of the base cidr used

- subnet_mask
    - type = number
    - default = 24
    - defines the subnet mask to be used within the network


## Outputs
- public_subnets
    returns list of public subnet ids

- private_subnets
	returns list of private subnet ids 

- vpc_id
	returns vpc id
  