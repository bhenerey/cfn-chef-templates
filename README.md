# Cloudformation templates with Chef Bootstrapping

Here's a whitepaper to get you started with [Integrating AWS CloudFormation with Opscode Chef](https://s3.amazonaws.com/cloudformation-examples/IntegratingAWSCloudFormationWithOpscodeChef.pdf)
I've uploaded some templates based on the [sample templates](http://aws.amazon.com/cloudformation/aws-cloudformation-templates/) provided, and am noting my customisations. 

### ec2instance-rabbitmq.json

This is pretty straightforward. My only change is in the AWS::EC2::Instance Metadata where 
I've given a role and tagged this instance with the Stackname. 

```json
  "/etc/chef/roles.json" : {
                "content" : {
                  "run_list": [ "role[rabbitmq]" ],
                  "tags": { "Ref": "AWS::StackName" }
                },
```

### autoscaling-fronends.json

### templates-within-template.json
