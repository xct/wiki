# Terraform

### Base Commands

```text
terraform init
terraform plan
terraform apply
terraform destroy
```

Note: Be careful when using conditionals like: `var.ENVIRONMENT == "deploy" ? 0 : 1` because if you pass in no environment variable at all it will default to deploy. So to reach the "not deploy" state you have to pass in something like `--var ENVIRONMENT=test` .

 



