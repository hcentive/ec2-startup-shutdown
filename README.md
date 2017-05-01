ec2-startup-shutdown
====================
A command line tool to startup and shutdown AWS EC2 instances based on start and stop times defined in instance tags.

Design
------
EC2 instances at [hCentive](http://www.hcentive.com "hCentive, Inc.") are tagged with the following resource tags -
* _Name_ (**required**) - name of the instance e.g wem-cvtdev.demo.hcentive.com
* _cost-center_ (**required**) - cost center for the instance e.g. wem
* _starttime_ - daily scheduled instance startup time in UTC e.g. 03:30:00
* _stoptime_ - daily scheduled instance shutdown time in UTC e.g. 15:30:00
* _owner_ - active directory username or email address of the instance owner e.g. jagdeep.singh
* _stack_ - application stack for the server e.g. qa
* _created_ - date of instance creation e.g. 22-07-2013
* _expires_ - date of instance expiry/retirement e.g. 31-01-2014

The **_starttime_** and **_stoptime_** tags define instance start and stop times.
If your instances use a different tag nomenclature, you can update tag keys in [ec2.yml](https://phabricator.demo.hcentive.com/diffusion/AWS/browse/master/ec2-startup-shutdown/config/ec2.yml "ec2.yml"). Keep in mind the tool requires at least the _Name_ and _cost-center_ tags.

Installation
------------
System requirements -
* Ruby 2.1.1
* The following packages must be installed on the system -
  * build-essential
  * zlib1g-dev
  * openssl
  * libssl-dev
  * libopenssl-ruby

ec2-startup-shutdown requires the following environment variables to be present:
```
export AWS_ACCESS_KEY_ID='...'
export AWS_SECRET_ACCESS_KEY='...'
export AWS_REGION='us-east-1'
```
If this utility is run from an EC2 instance launched with an [IAM role](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html), the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` are picked up from instance metadata.

Usage
-----
ec2-startup-shutdown utility has the following commands. Type help to get the list of commands.

```
$ ec2 help
Commands:
  ec2 help [COMMAND]                                                     # Describe available commands or one specific command
  ec2 list COST-CENTER STACK -c, --cost-center=one two three             # Describe instances, with state, for a cost center (e.g. techops, shared-services, phix) and stack (e.g. dev, qa, sit)
  ec2 audit_tags COST-CENTER STACK -c, --cost-center=one two three       # Audit instance tags; add missing tags. Expects at least the 'cost-center' tag  
  ec2 start_instances COST-CENTER STACK -c, --cost-center=one two three  # Start stopped instances marked to be started at the hour for a stack e.g. dev, QA, SIT
  ec2 stop_instances COST-CENTER STACK -c, --cost-center=one two three   # Stop running instances marked to be stopped at the hour for a stack e.g. dev, QA, SIT
```

* ### ec2 list
List instances, with state, for a cost center (e.g. techops, shared-services, phix) and stack (e.g. dev, qa, sit).
###### USAGE
`ec2 list COST-CENTER STACK -c, --cost-center=one two three`
###### OPTIONS
`-c, --cost-center=one two three  # cost-center - space separated list of cost center for the instances`
`-s, [--stack=one two three]      # stack - space separated list of stack for the instances`

* ### ec2 audit_tags
Audit instance tags; add missing tags. Expects at least the 'cost-center' tag
###### USAGE
`ec2 audit_tags COST-CENTER STACK -c, --cost-center=one two three`
###### OPTIONS
`-c, --cost-center=one two three  # cost-center - space separated list of cost center for the instances`
`-s, [--stack=one two three]      # stack - space separated list of stack for the instances`
`-d, [--dry-run]                  # dry run - does not fix tags; default: false`
`-y, [--no-prompt]                # no-prompt - automatic yes to prompts; default: false`
* ### ec2 stop_instances
Stop running instances marked to be stopped at the hour for a stack e.g. dev, QA, SIT
###### USAGE
`ec2 stop_instances COST-CENTER STACK -c, --cost-center=one two three`
###### OPTIONS
`-c, --cost-center=one two three  # cost-center - space separated list of cost center for the instances`
`-s, [--stack=one two three]      # stack - space separated list of stack for the instances`
`-d, [--dry-run]                  # dry run - does not stop instances; default: false`
`-n, [--notify-owner]             # notify - notify owner before stopping instance; default: true`
`-y, [--no-prompt]                # no-prompt - automatic yes to prompts; default: false`
* ### ec2 start_instances
Start stopped instances marked to be started at the hour for a stack e.g. dev, QA, SIT.
###### USAGE
`ec2 start_instances COST-CENTER STACK -c, --cost-center=one two three`
###### OPTIONS
`-c, --cost-center=one two three  # cost-center - space separated list of cost center for the instances`
`-s, [--stack=one two three]      # stack - space separated list of stack for the instances`
`-d, [--dry-run]                  # dry run - does not start instances; default: false`
`-n, [--notify-owner]             # notify - notify owner before starting instance; default: true`
`-y, [--no-prompt]                # no-prompt - automatic yes to prompts; default: false`
