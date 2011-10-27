# Cloudformation templates with Chef Bootstrapping

Here's a whitepaper to get you started with [Integrating AWS CloudFormation with Opscode Chef](https://s3.amazonaws.com/cloudformation-examples/IntegratingAWSCloudFormationWithOpscodeChef.pdf). 

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

### autoscaling-frontends.json

An autoscaling group is quite similar, except the Userdata which performs the bootstrapping is located in the AWS::AutoScaling::LaunchConfiguration section. 

```json
"LaunchConfig" : {
      "Type" : "AWS::AutoScaling::LaunchConfiguration",
      "Properties" : {
        "UserData" : { "Fn::Base64" : { "Ref" : "WebServerPort" }},
```

### templates-within-template.json

This is pretty amazing. You can use CFN templates to create CFN stacks, and pass parameters to the child stacks. This is like 
Lego blocks for your infrastructure, which combined with Chef seems infinitely great. The only drawback is currently CFN does everything sequentially,
so I tend not to use this feature and create my stacks in parallel.

```json
  "Resources" : {
    "RabbitMQ": {
      "Type" : "AWS::CloudFormation::Stack",
       "Properties" : {
	     "TemplateURL" :  "https://s3.amazonaws.com/<bucket>/<template>.json",
	     "KeyName" :          { "Ref" : "KeyName" },
         "InstanceType" :     { "Ref" : "InstanceType"},
         "EC2SecurityGroup" : { "Ref" : "EC2SecurityGroup" },
	     "ChefClientUser" 	: { "Ref" : "ChefClientUser" },
	     "HostKeys" 		: { "Ref" : "HostKeys" },
	     "BucketPolicy"		: { "Ref" : "BucketPolicy" }
       }
    }
```
