# CloudFormation AWS Specific Parameters
---
- Default parameters types (Numbers, Strings and Tuples) need to have strict allowed values or patterns check
- AWS Specific Parameters allow to check the validity of a certain parameter and existence and availability of the required resource

## 
1. Start with provisioning prerequisites stack:

        aws cloudformation deploy \
                           --stack-name prerequisites \
                           --template-file 01-AWSSpecificParametersPrerequisites.yaml
        
2. One the stack is deployed, get the values of the stack outputs:
  
        aws cloudformation describe-stacks \
                           --stack-name prerequisites \
                           --query 'Stacks[].Outputs[][]'

3. Place the stack outputs to the parameter overrides file `02-AWSSpecificParameterOverrides.txt`. Note that we don't create SSH key as a part of a stack. You need to create it yourself and add its name in the text file.
4. Deploy the second stack using AWS Specific Parameters:

        aws cloudformation deploy \
                           --stack-name awsspecific \
                           --template-file 02-AWSSpecificParameters.yaml \
                           --parameter-overrides $(cat 02-AWSSpecificParameterOverrides.txt)
 
5. Clean up:

        aws cloudformation delete-stack --stack-name awsspecific
        # Wait until it is deleted.
        aws cloudformation delete-stack --stack-name prerequisites

If something went wrong with the stack deployment check if you used the right Subnet Ids for the private and public subnets.
Keep in mind that the first and the second stack must be in the same region and AWS account.
 
P.S. For using resources from one stack in another you can also use Stack Exports.