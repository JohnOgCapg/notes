## cloud formation basics
What formats can the Cloud Formation file be in; YAML and JSON
What does CloudFormation focus on (whats does the file  contain); Logical resources (ie a template that becomes a stack)
What does a cloud formation stack create ; Physical resources
Does updating / applying a cloud formation stack change physical resources; If need be


## non portable template
Why cant you reuse a cloud formation template if hardcode bucket names ; Bucket names have to be unique -> include stack name in bucket name
Are AMIs region specific; Yes
How to use AMI Ids in cloud formation templates; Provide a per-region list

## template and psuedo parameters
What do cloud formation template parameters do; Allow the user to pass config / variables like name and size into a script
How pass variables into a cloud formation script; Template parameters
Can you provide drop downs, defaults & stuff for template parameters in cloud formation; Yes
Can you provide types and validation for template parameters in cloud formation; Yes
What tag to do validation of template parameters in cloud formation; AllowedValues [list]
What are psuedo parameters in cloud formation; Parameters provided by AWS / metadata, eg AWS::Region
Example of psuedo parameters in cloud formation; AWS::stackID, AWS::stackName and AWS::AccountID

## intrinsic functions
What are ref and GetAtt, or join and split examples off in cloud formation; Intrinsic functions
In cloud formation what intrinsic function to use to reference a resource from another (eg put a subnet in a VPC you created; Ref and GetAtt
In cloud formation what intrinsic function to use to manipulate text string chop: stitch; Join and Split
In cloud formation what intrinsic function to use to get a list of Azs and pick one; GetAZs and Select
In cloud formation what intrinsic function to use to do conditionals; If AND Equals Not Or
In cloud formation what intrinsic function to use to encode b64; Base64
In cloud formation what intrinsic function to use to do text substitutions; Sub
In cloud formation what intrinsic function to use to build subnet blocks & what do you pass in; Cidr - you pass in the source CIDR block, how many subnets you want and how many bits per block (ie if you pass 12 bits, it builds /20s)
In cloud formation what intrinsic function to use to manipulate lists; FindInMap
In cloud formation what intrinsic function to use to change text strings; Transform
In cloud formation  how to get current version of an AMI as pased in as a parameter called LatestAmiId; !Ref LatestAmiId
In cloud formation how to geta logical resource; !Ref GetAttr
In cloud formation what does getAzs really return; List of Azs in which the default VPC of the region has subnets
In cloud formation what do you have to do to user data to pass it in; Base64 encode it with the Base64 intrinsic function
In cloud formation can you self reference an instance in user data; Nope = instance ID isn't valid at point it is passed in
In cloud formation when you ask CIDR for size 12 subnets do you get /12s or /20s; /20s

## mappings
What do mappings in cloud formation do?; Mappings link keys to values allowing lookups - you provide key get value
How many nesting levels do cloud formation mappings have;  Mappings can also be multi level, eg level 1 = region, level 2 = 2 CPU architecture for looking up AMI IDs
How do you do a lookup in a cloud formation map; Use the !FindInMap intrinsic function and pass a map name, first key and optionally a second key

## Outputs
Is the outputs section of cloud formation optional? ; yes - entirely
How do you show responses from cloud formation, eg resource names?; use outputs
Can you pass outputs from one  cloud formation stack to a parent? ; yes outputs
How do you pass outputs from one cloud formation stack to another non parent?; cross stack references
What are the parts of a cloud formation output? ; the name and the value
Should you do explicit naming for S3 buckets in cloud formation? ; no hard naming it = you can only ever provision the stack once cos names must be unique, so don't explicitly name it, but grab its name as an output 
If you don't name an S3 bucket in a cloud formation stack what does AWS call it?; stack name + bucket + random chars
How do you force a user to pick a valid key in cloud formation?; set the parameter to be of type key, AWS will grab the available keys & give the user a drop down

## Parameters
How pass parameters from SSM Parameter Manager into cloud formation?; "AWS::SSM::Parameter::Value" tells CF its a value from parameter store and this tells it which one <AWS::EC2::Image::Id>

## Misc
Are SSH keys regional?; yes but you can have different keys with same name in multiple regions
Does instance connect and session manager need SSH keypairs to be set up in region / on your laptop?; no (eek!)

## Conditions
How make a stack react to stuff? ; use conditions
Is the conditions section optional in cloud formation?; yes
When is the conditions block in cloud formation evaluated?; right at the top, before anything gets created
What do cloud formation conditions evaluate to?; true or false
Can conditions use the logical intrinsic functions like AND EQUALS IF NOT OR?; yes
What conditions get create in cloud formation?; anything that evaluates down to true
Typical uses for cloud formation conditions?; environment type (prod, dev, test) and number of AZs (2,3,6)
How use conditions in cloud formation?; set up a conditions block with the logic to evaluate to True or False. Then in the resources section use condition: {name} on the resource and only the resources with a TRUE conditions block get created
Can you nest conditions in cloud formation?; yes

## Depends On
Does cloud formation by default create resources in parallel?; yes
Is cloud formation ok at understanding dependencies, like cant create subnet until VPC its in is created?; yes - but you also have depends on
How do you get cloud formation to know it cant create one resource until another is created?; use DependsOn
Do you need to use depends on in cloud formation when attaching an IGW to a VPC?; no- but the demo used this (CF does an implicit dependency)
Why do you need to use depends on in cloud formation when attaching an EIP to a EC2 instance?; common exam Q - this depends on the IGW being attached to the VPC
Secondary benefit of using depends on in cloud formation?; helps when deleting the stack
What does depends on in  cloud formation actually wait for?; on create: the specified resource must be complete, on delete, this resource must be zapped first
Can you specify multiple resources in a depends on in cloud formation?; yes

## Wait Conditions and CNF SIGNAL
Why need completion state logic in  cloud formation?; bootstrapping can take a while & is happening AFTER the create complete point is reached in cloud formation
How should bootstrapping wait/tell cloud formation it has completed?; CNF signal
What's the longest you can have cloud formation wait for bootstrapping?; 12 hours
What does cloud formation do if it never gets a positive signal from a bootstrapper its waiting on?; times out and fails the resource create
Can cloud formation signal send success and failure notes (from bootstrapping)?; yes both
How should you wait on EC2/ASG resources getting boot strapped in cloud formation?; use a creation policy
How should you wait on other (not EC2) resources getting boot strapped in cloud formation?; use a wait condition
What is the difference between a wait condition and a creation policy when handling dependencies in cloud formation?; Creation policy is for EC2/ASG resources, Wait Condition is for other kinds of resources
What's in a creation policy when doing dependencies in cloud formation?; how many positive signals it needs and the time out
What is a wait condition in cloud formation?; a logical resource that doesn't hit create complete until the things in the wait handler complete, and includes a pre signed URL for things to tell it they are ready
How do you tell a wait condition in cloud formation you are ready?; you hit its wait condition handler URL and send good (ie from the resource your are creating)
Can wait conditions in cloud formation require multiple positive signals?; yes
Do wait conditions in cloud formation time out?; yes

## Nested Stacks
Are cloud formation stacks isolated / self contained by default?; yes unless you do nested or cross stack references
How many resources can a cloud formation stack contain?; 500 (hard limit)
Can you reference resources in other independent stacks in cloud formation?; no (has to be a nested or x-stack reference)
What is a root stack in cloud formation?; the first stack in a nested stack - ie the one you actually provision, and that calls other stacks
Can nested stacks contain further nested stacks in cloud formation?; yes
Do you do anything to make a stack nested in cloud formation?; no
Can you pass parameters into a nested stack in cloud formation?; yes the parent passes them in when it declares the nested stack
Do you have to pass parameters into a nested stack in cloud formation?; no not if it doesn't need them or has defaults
Can you reference resources inside a nested stack in cloud formation?; no only things that have output parameters
Can you pass outputs from one nested stack into another nested stack in cloud formation?; yes
Why use nested stacks in cloud formation?; makes stacks more modular and reusable
Difference between cross stacks references and nested stacks in cloud formation?; with nested stacks everyone is talking to the same resources managed under the parent so if you provision the stack twice you get 2 sets of everything, with cross stack references the resource are all singletons so if you provision stacks twice you're still talking to the same singleton
When use nested stacks in cloud formation?; when you're building a single project and your want all resource to create/delete as a single whole
What kind of cloud formation multi-stack design do you use if you want all resources to be created/deleted together?; nested stacks
What kind of cloud formation multi-stack design do you use if you want resources to live longer/shorter (like reuse a VPC for various transient projects)?; cross stack references

## Cross Stack References
How do you access resources in some other, unconnected, not-nested stack without doing static / hard coded things?; cross stack references
Are cloud formation stack outputs visible from other stacks?; no only to parent stacks (unless doing cross stack references)
How do cross stack references in cloud formation make outputs available?; they use exports, eg VPC ID, security group ID, subnet ID
How do you get access to a previously exported cross stack reference in cloud formation?; you import them (like a parameter)
Can you do cross region cross stack references in cloud formation?; no
Can you do cross account cross stack references in cloud formation?; no

## Stack Sets
What do stack sets do in cloud formation?; Allow you deploy stacks across many accounts and many regions
What are stack sets deployed to in cloud formation?; an admin account (where it lives) from where it deploys stack instances to specific regions and accounts 
In cloud formation what are stack instances?; the things the stack set creates in each account/region when you deploy the stack set (in target accounts)
In cloud formation what are target accounts?; the accounts the stack set deploys stack instances to
How do stack sets get permission to create stuff?; self managed (roles you create) and service managed roles
What are service managed roles in cloud formation?; roles cloud formation automatically creates for you so it can create stack instances in each target account
In stack sets what is the admin account?; the account you create the stack set in
In stack sets what's the difference between admin and target account?; 1 admin where the stack set lives, many targets where the stack instances live
What are concurrent accounts within stack sets?; how many accounts to be working in at a time (more=deploy faster)
What is failure tolerance in stack sets?; how many target accounts are allowed to fail before it rolls back the stack set as a whole
What is retain stacks in stack sets?; whether the stack instances get zapped when you delete the stack set (by default retain = no)
Common scenarios for stack sets?; enable/set up config in all accounts, create IAM roles for cross account access

## Deletion Policies
Why use a delete policy in cloud formation?; stops data loss from EBS and RDS when stacks are deleted
What delete policies can you specify in cloud formation?; delete, retain and (if supported) snapshot
What is EENRR when it comes to snapshot delete policy?; the acronym for supported services EBS, Elasticache, Neptune, RDS, Redshift
What resources support the snapshot delete policy?; EBS, Elasticache, Neptune, RDS, Redshift
What does snapshot delete policy do?; takes a snapshot (you get to delete that, or pay for it) then deletes it 
What does delete policy NOT cover?;  UPDATES or REPLACES -> if your CF code does a replace, your data might get zapped

## Stack Roles
What permissions for cloud formation use when creating a stack by default & what's it mean?; permissions of the user provisioning the stack, so the user needs permissions to interact with resources (ie be admin)
Can cloud formation use other permissions than those of the user doin the provisioning + why?; yes stack roles, so user doesn't need admin permissions
How are roles used with stack roles? ; The stack role (with all required permissions) are attached to the stack so anyone can then provision the stack (user doesn't need permission to create stuff & they cant change or use the stack role themselves)

## CFN HUP
What is cloud formation HUP?; helper to tell an instance to rerun init to reapply desired state (either for drift fix, or cos you updated desired state)

## CFN INIT
What is PGU SFC S?; acronym for cloud formation directives: packages (to install), groups and users (to create), sources (to download and extract), commands to execute, services (to enable)
What is cloud formation Init used for?; set of directives in a template in the logical resource that spec what happens to the instance (alternative to user data)
How does cloud formation init differ from user data?; init is directive/desired state based (you say what) so works cross platform (win and linux) whereas user data is run a script (you say waht+ how)
Where does cfn-init helper script get placed?; on the ec2 instance being provisioned & like puppet it applies the desired state config from the stack
How does cnf-init helper script get called?; via user data which calls init then waits to signal
Is cnf-init idempotent / can you run it again and again?; yes its just desired state
Is user data idempotent / can you run it again and again?; nope only gets run at instance create
What is a cloud formation configuration directive?; the desired state when using cfn init
What goes into a cloud formation configuration directive?; packages (to install), groups and users (to create), sources (to download and extract), commands to execute, services (to enable)
Can you have multiple cloud formation initi configuration directives?; yes lots all following  PGU SFC S
What is a config set in cloud formation?; a set/grouping  of config directives (collection of directives to apply)
What is the purpose of CNF Signal, Init and HUP?; SIGNAL is used via use data to say "resource is completed bootstrapping", INIT is an alternative to user data that does directive based boot strapping & configuring (but is done only once), HUP is used to tell the instance to rerun init
How many times does cloud formation INIT get run?; once at create, then whenever HUP is signaled

## Joining up init, hup and signal
Does cloud formation bootstrapping with user data flag resource complete before or after the user dat scrip completes?; if you don't use signal, it will flag it as complete as soon as user data is CALLED, not when it COMPLETES
Does changing user data in cloud formation impact the instance?; yes instance gets stopped when you apply update, BUT user data DOESNT get rerun
How use cloud formation signal?; put a create policy on the ec2 instance (time out and num signals), then at the end of the user data script call cloud formation signal
How many times does user data get executed from cloud formation?; only ever once on stack create, it never gets rerun if you update/reapply the stack (if want this, you need to use cnf init and cfn hup)
How do you use cnf init (3 steps)?; call cfn init then signal from user data, put a create policy on the resource so signal works, then put the config directives in so init knows what to configure
How debug user data commands?; /var/log/ cloid-init.log and cloud-init-output.log
How to debug cnf init ?; SSH on to the provisioned box, look in /var/log/ cfn-init.log, cfn-init.cmd.log 
What is the difference between cnf init logs and cloud-init logs?; cnf init what init did (outputs and commands), whereas cloud init = debug what user data did (ie call cfn init)
List the 2 kinds of log files used with debugging bootstrapping in cloud formation?; cnf-init and cloud-init for cfn int and user data respectively 
how does cnf HUP work?; have a config showing how often to check for changes, and what happens if it sees an update (ie rereun cfn init)
Does reapplying a stack cause HUP to run?; no it only changes the resource, so that the next time the HUP daemon triggers it will pick up/apply the changes - so be careful

## Change Sets
What 3 levels of impact when applying a cloud formation stack?; no interruption, some interruption, replacement
What is a cloud formation change set?; run an updated stack in preview mode to see what it would do, then decide whether to keep/apply
Are change sets always applied in cloud formation?; no you get the choice whether to apply once you see what it would do
Can you execute a change set in cloud formation?; yes if you decide its good
How create a change set in cloud formation?; from the change set tab under the stack
Can you apply part of a change set?; no doesn't look like it

## Custom Resources
Does cloud formation support everything in AWS?; no, you need to use custom resources for things that are unsupported or external
Example of need to use cloud formation custom resources?; putting things in buckets, creating external resources, downloading things from external
How use custom resources in cloud formation?; you send an event (eg SNS or HTTP/Lambda) to the specified endpoint
Can you get responses from cloud formation custom data?; yes any outputs come back into cloud formation
Can you delete non empty s3 buckets via cloud formation?; no, should use a custom resource handler to zap the bucket contents (cant delete non empty buckets)
How do you populate s3 buckets from cloud formation?; use a custom resource eg lambda function to download things into the bucket (and an implicit "depends on" which CF adds so the bucket gets provisioned first, then the custom resource that populates it gets executed & vice versa on delete
How many parts to a custom resource do you need?; 2. The create code and the delete code
What's the ARN of the handler function called in cloud formation custom resources?; The service token 
Why do you generally need a custom resource when creating s3 buckets from cloud formation?; so the custom resource handler can empty the bucket when the stack gets deleted
How do you delete the contents of an S3 bucket the easy way?; press empty bucket on the bucket's console page
How many methods does a cloud formation custom resource handler need?; only 1, it gets passed an event that includes whether its a create or delete
How do you declare a custom resource in cloud formation?; use Type: "Custom::{thing}" in the declaration, e.g. Type: "Custom::S3Objects"
What's a service token in cloud formation custom resources?; the ARN of the handler function
What's the only mandatory bit of the properties section in a cloud formation custom resources & what's it do?; the service token, which is the ARN of the handler
What happens to the properties declared on a custom resource in cloud formation?; they get attached to the event that's passed to the handler
How do you attach permissions to the handler function in a custom resource in cloud formation? ; you attach/declare a role in CF alongside the custom resource & this becomes the lambda execution role
How does a custom resource (lambda) in cloud formation signal its done its thing & CF can carry on?; CF recognizes an implicit dependency, ***IF*** you pass the the thing (ie the s3 bucket)  the custom resources is populating into the function, so it knows bucket first, then custom resource (on create, reverse on delete)
What's the order things get created when using a cloud formation custom resource to populate a bucket?; bucket, lambda role, backing lambda function, custom resource (ie exec lambda)
How do you debug the lambda backing a cloud formation custom resource?; check the execution logs for the function in cloudwatch logs
What's the order things get deleted in when using a cloud formation custom resource to populate a bucket?;  custom resource (ie exec lambda), backing lambda function, lambda role, bucket
What log group(s) do the lambda functions backing a cloud formation custom resource get written to?; there is one log group per custom resource handler per stack. streams will depend when the owning stack got provisioned

## Drift Detection
If you reapply a stack will it do drift detection & correction?; yes things might get put back (or replaced - beware data loss!)
Can you get a drift detection report from cloud formation?; view the stack then do stack actions / detect drift
What's in a cloud formation drift detection report?; list of resources in the stack & a not saying in synch or modified 
Does drift detection in cloud formation give detailed info (ie specifically what each drift is)?; yes there is a full drill down
How do you correct drifts in cloud formation?; either change the resources back & rerun, or update the stack definition in YAML & rerun
What can you do when wanting to do a cloud formation stack update?; update with same stack file, upload a new stack file
Does adding a delete policy cause any deleting in cloud formation?; no - just adds the protection so is a good things to do before zapping (or when originally provisioning)
What does delete in cloud formation do when deleting something with delete protection?; delete stack works, but shows as delete skipped for the protected resources, which continue to exist
What is a cloud formation "import resources into stack" operation?; you upload a stack, then match physical resources to the logical
When doing a cloud formation "import resources into stack" operation, do you upload a stack file?; yes, which is what you start matching from 
How does a cloud formation "import resources into stack" operation work?; it lists the logical resources in the uploaded stack, and ask you what physical resource (instance IDs) each maps onto
Does a cloud formation "import resource into stack" change any resources?; yes it might apply some tags & it creates the association between existing physical resources and the logical resources in the stack
Does a cloud formation "import resource into stack" fix drift detections between as-is physical and the requested logical resources?; no it only create associations & tags. You want drift fixing, you then have to do an update stack
