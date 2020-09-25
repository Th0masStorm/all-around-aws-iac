CloudFormation Services Roles
---
- Used for granting CFN its own privileges instead of using user's permissions
- Is attached to the stack during creation, can not be detached, but it is possible to change during stack operations (Update and Delete)
- User running stack operation must have `iam:PassRole` and `cloudformation:*` permissions
- Implementation must follow Least Privilege Principle
---
Create the service role stack:
```bash
aws cloudformation deploy --stack-name ServiceRole \
                          --template-file 01-ServiceRole.yaml \
                          --capabilities CAPABILITY_IAM
```
Get the Role ARN:
```bash
ROLEARN=$(aws cloudformation describe-stacks --stack-name ServiceRole \
                                             --query "Stacks[0].Outputs[0].OutputValue" \
                                             --output text)
```

Create the first stack (it will work with no issue).
```bash
aws cloudformation deploy --stack-name WillCreate \
                          --role-arn $ROLEARN  \
                          --template-file 02-WillCreate.yaml
```

Create the second stack (it will fail).
```bash
aws cloudformation deploy --stack-name WillNotCreate \
                          --role-arn $ROLEARN \
                          --template-file 03-WillNotCreate.yaml
```

Delete the Service Role stack and then the `WillCreate` stack.
```bash
aws cloudformation delete-stack --stack-name ServiceRole
# Wait until it's deleted
aws cloudformation delete-stack --stack-name WillCreate
```

`WillCreate` fails to delete. We need to create a new role to overwrite the deleted one.
```bash
aws cloudformation deploy --stack-name ServiceRole \
                          --template-file 01-ServiceRole.yaml \
                          --capabilities CAPABILITY_IAM
ROLEARN=$(aws cloudformation describe-stacks --stack-name ServiceRole \
                                             --query "Stacks[0].Outputs[0].OutputValue" \
                                             --output text)
aws cloudformation delete-stack --stack-name WillCreate \
                                --role-arn $ROLEARN
```
