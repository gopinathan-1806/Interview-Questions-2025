## Terraform Import block

If you want to import bunch of EC2 or any resources which was created manually into Terraform, we need to follow the TF import block.

Write the import block first

```
import {
  to = aws_instance.web
  id = "i-0abc123456789def0"
}

resource "aws_instance" "web" {
}
```

And then run TF plan config generator

``` terraform plan -generate-config-out=generated.tf ```

Terraform creates state file for that resource

https://www.youtube.com/watch?v=TJlbXUKgBHg

## What is Terraform Taint?

Terraform Taint marks a resource for recreation. During the next terraform apply, Terraform destroys the existing resource and creates a new one, even if there are no configuration changes. 

In newer Terraform versions, terraform taint is deprecated and replaced by the -replace option.

``` terraform apply -replace="aws_instance.web" ```
