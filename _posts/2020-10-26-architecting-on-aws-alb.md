---
title: "Architecting on AWS: ALB"
sub_heading: "Simple and effective load balancing"

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

Picking up from [where we left off](/2020/10/21/architecting-on-aws-ec2/#a-job-well-done-or-not), we need eliminate the single point(s) of failure in our architecture. Before we get a chance to do that though, we're made aware of another issue: Our web server's hostname (URL) changed too.

Regardless of whether these issues are related, perhaps we can think of a solution that solves both issues? Let's see what we can find.

## Changing hostname

After a quick search, [we find the reason](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#concepts-public-addresses) for our changing hostname: _"We release the public IP address from your instance, or assign it a new one."_   Ah ha! Perhaps our instance was _"stopped, hibernated, or terminated"_ which caused it to obtain a new public IP address.

Now that we know what the problem is, how do we solve it? Lucky for us, the solution is [in that same document](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#ip-addressing-eips). _"An Elastic IP address is a public IPv4 address that you can allocate to your account. **You can associate it to and disassociate it from instances as you require.**"_ Perfect! Attaching an Elastic IP to our EC2 instance enables us to ensure it will have a static IP address/hostname. 

## 2 instances, 2 hostnames

But wait a minute... if we deploy our second server and assign it an EIP, we'll end up with two separate hostnames:

<p align="center"><img src="/assets/2020/10/ec2_website_2_hosts.svg" /></p>

That means that if a server fails (e.g `webserver1.example.com`), we'll need to ask visitors to browse to the remaining hostname (e.g `webserver2.example.com`). That won't do! We want to provide a seamless experience. Therefore, EIPs aren't the right solution for us. Back to the drawing board we go...

## Application Load Balancer (ALB)

The next possible solution we find are called Application Load Balancers. During our research on them, we realise that they can solve **both** our redundancy issue and our dynamic hostname issue too! 

In regards to redundancy, an ALB allows us to seamlessly deploy servers across Availability Zones. This means that our website will stay online even if AWS suffers another Availability Zone outage.

In regards to dynamic hostnames, ALBs actually have static hostnames. That means it doesn't matter if our web servers come and go. Visitors will only ever need to remember a single web address. 

So that settles it! An ALB is the way to go. Let's get to work on updating our CloudFormation template.

## All done!

After [updating our template](https://github.com/OzNetNerd/Architecting-on-AWS/blob/main/EC2_Website_ALB/ec2_website_alb.yaml), we're ready to launch the stack:

```
aws cloudformation create-stack \
--template-body file://ec2_website_alb.yaml \
--stack-name OzNetNerd-demo-environment
```

__Note:__ Replace `create-stack` with `update-stack` if you didn't delete the stack after completing the previous task.
{: .notice--primary}

__Note:__ The CloudFormation template provides two optional parameters. They can be used to lock down access to the EC2 instance as well as provide SSH access to it. See the [README](https://github.com/OzNetNerd/Architecting-on-AWS/tree/main/EC2_Website_ALB) for more information.
{: .notice--info}

Once our deployment completes, it will look like this:

<p align="center"><img src="/assets/2020/10/ec2_website_alb_diagram.svg" /></p>

## Testing

Now that the stack has been updated, it's time to put our new design to the test. First we retrieve the ALB's hostname using the following command:

```
aws cloudformation  describe-stacks --stack-name OzNetNerd-demo-environment \
--query "Stacks[0].Outputs[?OutputKey=='Ec2WebHostB'].OutputValue" --output text
```

When we browse to the URL, we're relieved to see that the Flask app is working. Better still, we find that each time we refresh our browser, the page is served by one of the two web servers. Fantastic! Our ALB is working perfectly.

## Upload attempt

As a final test, we upload a file to the app... 

[![](/assets/2020/10/alb_upload.png)](/assets/2020/10/alb_upload.png)

Unfortunately see that our `data.csv` isn't listed under **"Uploaded Files"**. What is going on here?

## Actually... there it is!

In a similar vein to _"switch it off and on again"_, we refresh our page hoping that it'll magically fix our issue. And it turns out, it does!

[![](/assets/2020/10/alb_upload_found.png)](/assets/2020/10/alb_upload_found.png)


## Investigation

After what feels like forever, we realise what the issue is. Our uploads are being uploaded to one web server. When we refresh our browser, we're then load balanced to the other server. As this server didn't receive the file, it shows an empty file list. When we refresh the browser again, we're then load balanced to the original server which **does** have the upload.

Looking at the screenshots above, we can see the following happen:

1. We browse to the ALB's hostname. It directs us to **Server A** (ec2-13-238-184-191) which presents us with the upload form. 
2. When we click "upload", the ALB directs us to **Server B** (ec2-3-24-218-53). This means that **the file is stored on Server B.**
3. When we refresh our browser, the ALB directs us to **Server A**. Because this server doesn't have the file, no files are listed under **"Uploaded Files"**.
4. When we refresh our browser again, the ALB directs us to **Server B**. This time we see the file listed.

## Solution?

Now that we know what the problem is, how do we fix it?