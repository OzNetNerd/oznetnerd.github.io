---
title: "Architecting on AWS: Infrastructure as Code (IAC)"
sub_heading: "I feel the need... the need for speed, consistency & efficiency!"

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
- Architecture

---

_This post is a part of the "[Architecting on AWS](/2020/10/17/architecting-on-aws/)" series_

Today's the first day at our new job! After meeting the team, we were given our first task: Set up a basic AWS environment.

While we could do this quite easily through the AWS console, a lot of engineers seem to be talking about using "Infrastructure as Code" instead. As this is a greenfield environment, we know that doing things the right way from the start will save a lot of blood, sweat and tears in the future.

So this leads us to the question...

## What is Infrastructure as Code?

It turns out, it's exactly what it sounds like - infrastructure that gets created through code. For example, the following [CloudFormation template](https://aws.amazon.com/cloudformation/getting-started/) creates an S3 bucket:

```
AWSTemplateFormatVersion: 2010-09-09
Description: S3 bucket example
Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    BucketName: my-example-s3-bucket-429342
```

<p align="center"><img src="/assets/2020/10/s3_bucket.svg" /></p>

OK that's great. We now know what IaC is, but this leads us to another question...   

## Why use Infrastructure as Code?

After all, we can do the exact same thing with the console. In fact, doing it through the console feels a lot easier too.

While that might be true, there are multiple reasons to use IaC instead of the console. To name a few:

* **Consistency:** IaC enables us to easily create identical dev & prod environments. Trying to do the same through "point and click" is error prone and becomes increasingly difficult to manage. 

* **Efficiency:** Using IaC, we test infrastructure changes in our dev environments. Once they've been validated, we can easily push these changes to prod.

* **Speed:** Instead of creating large "point and click" change documents, changes and can be done using Git, Pull Requests and code reviews.

OK great, we're now convinced that IaC is the way to go. This leads us to our final question...

## Which do I choose?

While researching IaC, we came across:
 
* CloudFormation
* CDK
* Terraform
* Pulumi 

...just to name a few. With so many options, how do know which one we should choose? 

While they're all great options, we choose to use CloudFormation. The reason being that it's very powerful, yet very easy to use.

As we can see, there's a lot of IaC options out there. The key is to **focus on one.** After you've got the hang of it, you'll find a lot of the knowledge you've gained can be used with the others. 
{: .notice--primary} 

## Let's get started!

As we're only looking to create the base infrastructure at this stage, we decide to create the following:

* **VPC** with CIDR 10.0.0.0/16
* Public **subnet** with CIDR 10.0.100.0/24
* Public **route table**
* **Internet Gateway**
* A default **route** from the Public subnet to the Internet Gateway

After a little trial and error, [we did it!](https://github.com/OzNetNerd/Architecting-on-AWS/blob/main/Base_Infrastructure/base_infrastructure.yaml) We completed our very first CloudFormation project!!!

To deploy our masterpiece, we simply [execute the following command](https://github.com/OzNetNerd/Architecting-on-AWS/tree/main/Base_Infrastructure):

```
aws cloudformation create-stack \
--template-body file://base_infrastructure.yaml \
--stack-name OzNetNerd-demo-environment
```

<p align="center"><img src="/assets/2020/10/base_infrastructure_diagram.svg" /></p>

__Note:__ The horse shoe shaped icon at the top of the diagram represents our Internet Gateway (IGW).
{: .notice--info}


## What's next?

As they say, no rest for the wicked! Our next task is in. 

The devs would like us to add a web server to the environment. Their app is written in Python and uses a web framework called Flask. They want us to spin up the server, install Python and Flask as well as allow incoming requests on TCP port 5000.

Let's get to work!

**Update:** The [next installment](/2020/10/21/architecting-on-aws-ec2/) has been posted.