---
title: "Architecting on AWS series"
sub_heading: "The best way to learn is through hands on experience"

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

---

In a [previous post](/2020/10/13/passed-3-aws-associate-exams-1-month/), I stressed the importance of hands on experience when it comes to preparing for AWS exams. However, people often object because "it's too hard". While they understand the theory, actually using the services is too daunting for them.

Unfortunately this doesn't bode well for them. Not only does it put them at risk of failing the exam, but it will severely affect their job prospects. Even if they're lucky enough to pass the exam, certs will only get them so far as explained in [this A Cloud Guru post](https://acloudguru.com/blog/engineering/how-many-certifications-do-i-need-to-get-a-cloud-job).

To assist people who find themselves in this situation, I'll be working on a new series of posts dedicated to getting hands on with AWS. 

## You've got the job!

Readers will play the part of a DevOps engineer who has been hired by a startup to build and maintain the company's AWS environment. To ensure it caters for all experience levels, we'll start with a single EC2 instance. We'll then add additional components as the company grows. 

Along the way we'll encounter real world problems. We'll then implement best practice solutions to solve these problems. As our skills mature, we'll adopt new services and technologies. We'll also refactor our environment and applications to increase availability and security while also reducing cost and administrative overhead in order to align with the [Well Architected Framework](https://aws.amazon.com/architecture/well-architected/).

## What's next?

Stay tuned! The first post in the series will be posted soon. In the meantime, please set up an AWS account and [enable a billing alarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html). This will ensure your lab spending is kept to a reasonable level.

## Posts in this series

1. [Architecting on AWS: Infrastructure as Code (IAC)](/2020/10/20/architecting-on-aws-infrastructure-as-code/)
2. [Architecting on AWS: EC2](/2020/10/21/architecting-on-aws-ec2/)
3. [Architecting on AWS: ALB](/2020/10/26/architecting-on-aws-alb/)