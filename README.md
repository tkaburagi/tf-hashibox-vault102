## Lab Environment (`terraform`)

The `terraform` directory contains configuration files for building the lab instances on AWS. Each student receives a machine that is preloaded with source code and running daemons of Vault, Consul, etc.

### Requirements

You will need the following:

- An IAM user and access keys with permission to create instances, IAM users, and IAM policies
- A paid or whitelisted [Atlas account](https://atlas.hashicorp.com/)

### Bootstrapping

**All commands should be run from inside the `terraform` folder!**

Prior to the start of the class, bootstrap the lab environment. You will need to know the total number of workstations to bootstrap and some other key information. Create a `terraform.tfvars` file based on the `terraform.tfvars.example` and fill all of the required
values from `variables.tf`.

AWS credentials should be provided with ENV vars such as:

```bash
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_DEFAULT_REGION
```

If you're using an AWS-managed key pair, and have downloaded the .pem file, you can retrieve the public key like so:

```
chmod 0600 <path-to-private-key.pem>
ssh-keygen -y -f <path-to-private-key.pem>
```

It is **highly** suggested that you specify a unique `training_username` and `training_password` that students will use to login to their workstations (all students share the same name and password). Leaving these at the default is a security risk.

```hcl
training_username = "butter-sprocket-9823"
training_password = "908&*-otesanthoeu-28loteg"
```

**NOTE: `public_key` is the contents of your public key file, not a path. This is for better compatibility with Terraform Enterprise.**

`owner` is the IAM username of the account who manages the lab. This can be used by Mozilla's Reaper tool to shut down instances overnight or shut down expired ones (documentation needed...read the [Reaper repo](https://github.com/mozilla-services/reaper) for now).

In general, you want to over-provision the number of workstations. This way if extra students join, or if there's an issue with one of the workstations, you can just pull another one from the pool.

There are other configuration options, but they have sane default values. If you get conflicts when provisioning, you may need to choose a different `cidr_block`. For most installations, this is unnecessary, but if you have multiple training sessions running simultaneously in the same AWS environment, you may need to tweak these values.

After you have filled out the information, run:

```shell
$ terraform init
```

This will download the required providers. Then run:

```shell
$ terraform plan
```

This will show you the output. The number of resources will be fairly large.
Next run:

```shell
$ terraform apply -auto-approve
```

This process can take some time depending on the size of the cluster. The output will contain a list of IP addresses to distribute to students.
