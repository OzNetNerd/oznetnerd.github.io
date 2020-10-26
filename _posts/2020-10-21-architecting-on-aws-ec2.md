---
title: "Architecting on AWS: EC2"
sub_heading: "They're like the VMs we all know and love, but they're hosted in the cloud"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps
- AWS
- Certifications
- Architecture

tags:
- DevOps
- AWS
- Certifications
- IaC
- Infrastructure as Code

---

_This post is a part of the "[Architecting on AWS](/2020/10/17/architecting-on-aws/)" series_

Picking up from where we left off, the [the devs' requirements](/2020/10/20/architecting-on-aws-infrastructure-as-code/#whats-next) were as follows:

1. They need a web server
2. Python & Flask need to be installed on the web server
3. Inbound traffic on TCP port 5000 needs to be allowed to reach the web server

OK great. Let's now take a look at how we can achieve this.

## Web server

After some research, we find our options include:

1. EC2 
2. ElasticBeanstalk
3. Lambda
4. ECS/Fargate
5. EKS
6. Lightsail
7. S3 static website


Wow... that's a lot of options. How are we going to choose one? Using the process of elimination, that's how.

Given that our devs are using Flask, we can immediately strike the "S3 static website" off the list. The reason being that it only serves HTML pages. We do not have access to the underlying web server and therefore cannot run Flask on it.

The next option we can strike off the list is Lightsail. As it is geared towards small businesses, it's likely that it won't suit the needs of our enterprise application.  

The next three we can strike off the list are Lambda, Fargate and EKS. The reason being that we haven't had a chance to get up to speed with containers and serverless.

Fantastic! We've whittled the list down to two options. But which do we choose? Elastic Beanstalk not only supports Python, but it provides automation and a flexibility out of the box. It sounds like the perfect option. 

After proposing Elastic Beanstalk as the way forward, the team advises that they'd prefer to go with EC2. As they're new to cloud too, they're worried about biting off more than they can chew. Utilising EC2 enables us to strike the perfect balance. We get the advantages of using cloud, while also using VM-like technology which we're already comfortable with.

__Note:__ We learned a very important lesson today - sometimes the most suitable solution isn't always the one which will be chosen. While we should continue to present the most suitable option(s) and suggest/provide training as required, the organisations' skills play a big part in the final decision.  
{: .notice--primary}

## Software installation

The next thing we need to figure out is: How are we going to install Python and Flask onto the server? After a little more research, we find three popular options:

1. Create custom AMIs with [Image Builder](https://aws.amazon.com/image-builder/)
2. Create custom AMIs with [Packer](https://learn.hashicorp.com/tutorials/terraform/packer)
3. Run the installation commands at launch using [UserData](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)

In the spirit of keeping things simple for this app, we choose UserData.

## Security

It appears we've left the best till last. Well, the simplest decision anyway.

By default, AWS gives us two methods to secure our VPC:
 1. Network ACLs (NACLs)
 2. Security Groups
  
By default, NACLs allow all inbound and all outbound traffic to traverse our VPC. Therefore in order to satisfy the devs' request, we'll need to attach a Security Group to the EC2 host. 

As Security Groups are stateful, all we need to do is enable the inbound traffic. The return traffic will be allowed out automatically.

## All done!

After completing the design, putting [the configuration together](https://github.com/OzNetNerd/Architecting-on-AWS/blob/main/EC2_Website/ec2_website.yaml) was a little easier than last time. As they say, practice makes perfect! 

To deploy our new configuration we simply execute the following command:

```
aws cloudformation create-stack \
--template-body file://ec2_website.yaml \
--stack-name OzNetNerd-demo-environment
```

__Note:__ Replace `create-stack` with `update-stack` if you didn't delete the stack after completing the previous task.
{: .notice--primary}

__Note:__ The CloudFormation template provides two optional parameters. They can be used to lock down access to the EC2 instance as well as provide SSH access to it. See the [README](https://github.com/OzNetNerd/Architecting-on-AWS/tree/main/EC2_Website) for more information.
{: .notice--info}

Once our deployment completes, it will look like this:

<p align="center"><img src="/assets/2020/10/ec2_website.svg" /></p>

## Testing

To get the website's URL, we simply get the `WebAddress` output from our CloudFormation stack:

```
aws cloudformation  describe-stacks --stack-name OzNetNerd-demo-environment \
--query "Stacks[0].Outputs[?OutputKey=='WebAddress'].OutputValue" --output text
``` 

After browsing to the URL, we find that the app is up and running. As a final test, we upload a file named `data.csv` and (thankfully!) see it gets stored on the server:

[![](/assets/2020/10/ec2-webserver-upload.png)](/assets/2020/10/ec2-webserver-upload.png)


## A job well done!... or not

Just as we were about to break open the champagne, **the  Availability Zone our instance was deployed in suffered a major outage.** Management has requested we implement a fix which will ensure this never happens again. 

Oh dear... how on earth are we going to do that?