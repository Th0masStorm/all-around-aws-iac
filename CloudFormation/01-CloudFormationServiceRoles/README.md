CloudFormation Services Roles
---
- Used for granting CFN its own privileges instead of using user's permissions
- Is attached to the stack during creation, can not be detached, but it is possible to change during stack operations (Update and Delete)
- User running stack operation must have `iam:PassRole` and `cloudformation:*` permissions
- Implementation must follow Least Privilege Principle
---
1. Create the service role stack:

        aws cloudformation deploy --stack-name ServiceRole \
                                  --template-file 01-ServiceRole.yaml \
                                  --capabilities CAPABILITY_IAM

2. Get the Role ARN:

        ROLEARN=$(aws cloudformation describe-stacks --stack-name ServiceRole \
                                                     --query "Stacks[0].Outputs[0].OutputValue" \
                                                     --output text)

3. Create the first stack (it will work with no issue).

        aws cloudformation deploy --stack-name WillCreate \
                                  --role-arn $ROLEARN  \
                                  --template-file 02-WillCreate.yaml

4. Create the second stack (it will fail).

        aws cloudformation deploy --stack-name WillNotCreate \
                                  --role-arn $ROLEARN \
                                  --template-file 03-WillNotCreate.yaml

5. Delete the Service Role stack and then the `WillCreate` stack.

        aws cloudformation delete-stack --stack-name ServiceRole
        # Wait until it's deleted
        aws cloudformation delete-stack --stack-name WillCreate

6. `WillCreate` fails to delete. We need to create a new role to overwrite the deleted one.

        aws cloudformation deploy --stack-name ServiceRole \
                                  --template-file 01-ServiceRole.yaml \
                                  --capabilities CAPABILITY_IAM
        ROLEARN=$(aws cloudformation describe-stacks --stack-name ServiceRole \
                                                     --query "Stacks[0].Outputs[0].OutputValue" \
                                                     --output text)
        aws cloudformation delete-stack --stack-name WillCreate \
                                        --role-arn $ROLEARN
