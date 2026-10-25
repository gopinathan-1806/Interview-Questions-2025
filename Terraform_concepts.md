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
