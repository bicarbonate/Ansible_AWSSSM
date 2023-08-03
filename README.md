# Ansible_AWSSSM

In a  nutshell:
Playbook that checks for prev registration, installs the agent, creates a one-off hybrid reg and registers Linux and WIndows

In more depth:
So I needed a playbook/template that would help us manage the SSM agent for deployed servers. Over the course of a few days 
I've ended up with this, somethingy ou shoudl know is that I used nested blocks. Probbaly don;t have to but it's my first time
nesting them so I went with it.

Requirements:
You are going to need to have an IAM user and role to use with this. You should be able to figure that out.
Environment variables for
1. AWS SSM ID
2. AWS SSM Secret Key
3. Ansible Controller, or another host that can perform the AWS CLI tasks
4. 

Block1

  Block2
  
1. Does a check to see if the target is already registered. It does this by checking for a directory with a name beginning in `mi-`
   in the right location for Linux (Ubuntu, Cent and RHEL), as well as Windows.
2. If found it's printed then the task for this host is killed by a Meta task.
   
  Block2
  
4. If not, then we delegate the running of `aws configure` to the Ansible Controller supplied with your AWS ID and Secret Key via 
   Environment variables.
5. We define a few facts:
   Activation Region - Via the first three letters of the hostname
   Location - Same as above
   Patch Group - Is determined by the os_type and os_family
6. Next we again delegate a command to the Controller; `aws ssm create-activation` where we are setting the region, description,
   calling a role, setting the reg limit to 1, and setting tags
7. Next we set two more facts: `code` and `id` from the output of the above create-activation (to be used later)
8. Here we are installing the agent, downloading it from the nearest region. Also based on os_type the appropriate .deb, .rpm, or .msi
   is downloaded and installed
9. Then we print the registration output, because I like seeing things like this in the job output
    
Block1

Rescue (invoked if the above tasks fail)

1. Under the Rescue section, here is where we deregister the client (both linux and windows)
2. Finally we delete the activation created above



