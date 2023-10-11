
# Post \#66958237 [Link](https://stackoverflow.com/questions/66958237/)

## Query aws to list all resources using boto3 python sdk

**Vote**: 1 (672/702) **Views**: 6882 (485/702) 

**Internal ID** \#1-3-220

Created at 2021-04-05 18:53:36

Tags: `python` `amazon-web-services` `boto3`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Is there a way to get all the resources in the aws account through python code using boto3. I went through the documentation, didn't find any list function which might solve this.


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-04-12 13:59:12

------------

Try this command.
but prerequisite enable `aws config` for this region before running this command.
```
import boto3

session = boto3.Session(profile_name=’your-profilename’)  
client = session.client(‘config’)  

resources = ["AWS::EC2::CustomerGateway", "AWS::EC2::EIP", "AWS::EC2::Host", "AWS::EC2::Instance", "AWS::EC2::InternetGateway", "AWS::EC2::NetworkAcl", "AWS::EC2::NetworkInterface", "AWS::EC2::RouteTable", "AWS::EC2::SecurityGroup", "AWS::EC2::Subnet", "AWS::CloudTrail::Trail", "AWS::EC2::Volume", "AWS::EC2::VPC", "AWS::EC2::VPNConnection", "AWS::EC2::VPNGateway", "AWS::EC2::RegisteredHAInstance", "AWS::EC2::NatGateway", "AWS::EC2::EgressOnlyInternetGateway", "AWS::EC2::VPCEndpoint", "AWS::EC2::VPCEndpointService", "AWS::EC2::FlowLog", "AWS::EC2::VPCPeeringConnection", "AWS::IAM::Group", "AWS::IAM::Policy", "AWS::IAM::Role", "AWS::IAM::User", "AWS::ElasticLoadBalancingV2::LoadBalancer", "AWS::ACM::Certificate", "AWS::RDS::DBInstance", "AWS::RDS::DBParameterGroup", "AWS::RDS::DBOptionGroup", "AWS::RDS::DBSubnetGroup", "AWS::RDS::DBSecurityGroup", "AWS::RDS::DBSnapshot", "AWS::RDS::DBCluster", "AWS::RDS::DBClusterParameterGroup", "AWS::RDS::DBClusterSnapshot", "AWS::RDS::EventSubscription", "AWS::S3::Bucket", "AWS::S3::AccountPublicAccessBlock", "AWS::Redshift::Cluster", "AWS::Redshift::ClusterSnapshot", "AWS::Redshift::ClusterParameterGroup", "AWS::Redshift::ClusterSecurityGroup", "AWS::Redshift::ClusterSubnetGroup", "AWS::Redshift::EventSubscription", "AWS::SSM::ManagedInstanceInventory", "AWS::CloudWatch::Alarm", "AWS::CloudFormation::Stack", "AWS::ElasticLoadBalancing::LoadBalancer", "AWS::AutoScaling::AutoScalingGroup", "AWS::AutoScaling::LaunchConfiguration", "AWS::AutoScaling::ScalingPolicy", "AWS::AutoScaling::ScheduledAction", "AWS::DynamoDB::Table", "AWS::CodeBuild::Project", "AWS::WAF::RateBasedRule", "AWS::WAF::Rule", "AWS::WAF::RuleGroup", "AWS::WAF::WebACL", "AWS::WAFRegional::RateBasedRule", "AWS::WAFRegional::Rule", "AWS::WAFRegional::RuleGroup", "AWS::WAFRegional::WebACL", "AWS::CloudFront::Distribution", "AWS::CloudFront::StreamingDistribution", "AWS::Lambda::Alias", "AWS::Lambda::Function", "AWS::ElasticBeanstalk::Application", "AWS::ElasticBeanstalk::ApplicationVersion", "AWS::ElasticBeanstalk::Environment", "AWS::MobileHub::Project", "AWS::XRay::EncryptionConfig", "AWS::SSM::AssociationCompliance", "AWS::SSM::PatchCompliance", "AWS::Shield::Protection", "AWS::ShieldRegional::Protection", "AWS::Config::ResourceCompliance", "AWS::LicenseManager::LicenseConfiguration", "AWS::ApiGateway::DomainName", "AWS::ApiGateway::Method", "AWS::ApiGateway::Stage", "AWS::ApiGateway::RestApi", "AWS::ApiGatewayV2::DomainName", "AWS::ApiGatewayV2::Stage", "AWS::ApiGatewayV2::Api", "AWS::CodePipeline::Pipeline", "AWS::ServiceCatalog::CloudFormationProvisionedProduct", "AWS::ServiceCatalog::CloudFormationProduct", "AWS::ServiceCatalog::Portfolio"]

for resource in resources:  
    response = client.list_discovered_resources(resourceType=resource)
    print(‘##################### {} #################’.format(resource)) 
    
    for i in range(len(response[‘resourceIdentifiers’])):
        print( ‘{} , {}’.format(response[‘resourceIdentifiers’][i][‘resourceType’], response[‘resourceIdentifiers’][i][‘resourceId’]))
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-04-05 19:31:56

------------

In boto3 you can use ResourceGroupsTaggingAPI method get_resources(). Which is used to get resources mainly based on tags but you can leave blank tag filter parameter and get all the resources supported.
Consider that not all resources are included and it is limited to a specific region but I hope that it can help you.
Examples:
Get all resources:
```
import boto3
client = boto3.client('resourcegroupstaggingapi')
client.get_resources()
```

Get resources of an especific service type:
```
import boto3
client = boto3.client('resourcegroupstaggingapi')
client.get_resources(
    ResourceTypeFilters=[
        'ec2:instance'
])
```


---


Official documentation:
[https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/resourcegroupstaggingapi.html#ResourceGroupsTaggingAPI.Client.get_resources](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/resourcegroupstaggingapi.html#ResourceGroupsTaggingAPI.Client.get_resources)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-12-07 13:27:37

------------

Following on from the answer Omar gave, I came up with the following:
```
from pprint import pprint

import boto3
from botocore.exceptions import ClientError

client = boto3.client('resourcegroupstaggingapi', )
regions = boto3.session.Session().get_available_regions('ec2')

for region in regions:
    print(region)
    try:
        client = boto3.client('resourcegroupstaggingapi', region_name=region)
        pprint([x.get('ResourceARN') for x in client.get_resources().get('ResourceTagMappingList')])
    except ClientError as e:
        print(f'Could not connect to region with error: {e}')
    print()
```

which will loop most regions and list any ARNs in that region, like so:
```
eu-north-1
[]

eu-west-1
['arn:aws:mq:eu-west-1:xxxxxxxxxxxx:broker:example:b-125099aa-8e22-462e-a8e9-bcc6b29c010a']

eu-west-2
[]

eu-west-3
[]
```



------------
    
    