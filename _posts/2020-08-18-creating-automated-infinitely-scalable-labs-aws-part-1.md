---
title: "Creating automated, infinitely scalable labs on AWS: Part 1"
sub_heading: "Automation and cloud: A match made in heaven"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps
- Python
- AWS
- Boto3

tags:
- DevOps
- Python
- AWS
- Boto3
- IaC
---

As a senior Sales Engineer for Trend Micro, I regularly run security PoCs, demos, webinars, etc. One day while running one of these sessions, I had an idea: 

_"People can learn a lot about **traditional security and DevSecOps** by becoming hackers themselves. Wouldn't it be great to give them an environment to hack, and then secure?"_

And with that, I started on the design immediately.

## High level design

After some time, the design was done:

1. **The entire solution must be automated. Therefore, everything must be done using:**
    1. Python
    2. Trend's APIs
    3. Boto3
    4. CloudFormation
2. **Don't re-invent the wheel:**
    1. Minimise the amount of code that needs to be written
    2. If AWS has a solution for it, use it
    3. If an open source tool or library suits, use it
3. **It must have a plugin in system**
    1. It must be easy for new hacks and tools to be added to the solution without needing to re-architect the solution
    2. Plugins with dependencies must ensure their parent plugin(s) are invoked beforehand
    3. Deployment cleanups must also adhere to the dependency tree
4. **It must be modular**
    1. Different users have different interests. The solution must enable lab scenarios to be added and removed easily
5. **Lab guides must be unique per user and generated automatically**
    1. Each user's unique username, password, URLs, etc must be populated in the guide
    2. The guides themselves must be completely customisable
    3. Distributing the guides must be simple        
6. **It must be extremely scalable**
    1. Deployments must be seamless, whether they're for 1 user or 1,000+ users
    2. User experience must be flawless, whether there's 1 user or 1,000+ users
7. **It must be portable & efficient**
    1. AWS quotas increases must be kept to a minimum to enable quick deployments in new accounts/regions
    2. It must be region agnostic so that it can be deployed in the region(s) which are closest to the users
    3. Re-use infrastructure where possible. For example, if two plugins use Cloud9, use a single instance instead of one per plugin  
8. **Users must not need to download or install anything on their machines**
9. **It must be secure**
    1. Users should not be able to access or affect each other's labs
    2. Secrets must not be passed or stored in clear text
10. **It must adhere to AWS limits, such as:**
    1. CloudFormation template file size (51,200 bytes for template body or 460,800 bytes for S3 objects)
    2. Number of resources per CloudFormation template (200)
    3. Parameters (60)
    3. Outputs (60)
    3. API rate limit
11. **It must be easy to deploy and clean up**
  
## Tooling

Once the design was done, choosing the tools which would make up the solution was the next task. This is how it went:

*  **Infrastructure as Code (IAC)**
    * CloudFormation (AWS) was chosen over Terraform (Hashicorp) as it affords vendor consolidation
        * AWS is able to provide the infrastructure, the Infrastructure as Code (CloudFormation) as well as the SDK (Boto3) 
    * Jinja2 was chosen over CDK to minimise complexity
    * Jinja2 was chosen over CloudFormation parameters and outputs to circumvent the 60 item limit on each
    * JSON templates were chosen over YAML templates because they can minified, thereby enabling more data per CloudFormation stack
    * Template uploads to S3 were chosen over body templates due to the increased file size limit (460,800 bytes vs  51,200 bytes)
    * Python will be used to split CloudFormation templates that exceed the maximum template size or maximum number of resources (200)
    * Python will be used to ensure AWS' API rate limits are respected
        * As CloudFormation, AWS CLI and AWS' SDKs (e.g Boto3) all have exponential backoff mechanisms built into them, this isn't typically a concern. However, as I'll cover in a future post, creating a large number of Cloud9 environments simultaneously will result in the rate limit being exceeded
* **Python**
    * Use boto3 to orchestrate the entire solution
    * Code will be documented using Google style docstrings, pdoc3 to generate the docs and type hints within the code
    * Black and pre-commit hooks will be used to enforce code style
* **User access**
    * IAM users, groups and policies
    * Cloud9 will enable users to take part without needing to install tools on their laptops
* **Secrets**
    * AWS Secrets Manager will be used to ensure secrets are handled securely
* **CI/CD**
    * Git on Cloud9
    * CodeCommit
    * CodePipeline
    * CodeBuild
    * CloudFormation
* **Containers**
    * Docker on Cloud9
    * Fargate
    * ECR
* **Kubernetes**
   * EKS via CloudFormation was chosen over eksctl to avoid needing to add another tool to the solution
* **Serverless**
    * Lambda
    * API Gateway
* **Lab guides**
    * CSS & HTML will be used as the base of the guides
        * This will enable the creation of themed labs (e.g virtual class rooms, hack days, etc)
    * Jinja2 will be used to:
        * Customise the guides for each user (username, password, Cloud9 URL, etc)
       * Provide `if` statements to ensure only relevant information & labs are included
    * WeasyPrint will convert the HTML guides into PDFs
    * Guides will be uploaded to an S3 bucket automatically
        * Presigned URLs will be generated automatically for easy distribution 
* **Lab configuration**
    * For ease of use, the entire solution will be driven out of a single YAML file
    * Sensible defaults must be provided to easy deployments (see below)
* **Infinite scalability**
    * STS and AWS Organizations will be used to scale the solution horizontally

## What does it look like?

This is what the config file looks like:

```yaml
---
lab_settings:
  workshop_name: ExampleLabs
  number_of_labs: 50
  aws_region: ap-southeast-2

plugins_config:
  conformity_existing_resources: {}
  conformity_template_scanner: {}
  application_security_lambda: {}
  application_security_container: {}
  workload_security_container: {}
  container_image_security: {}
```

As you can guess, the `lab_settings` section is used to set the name of the workshop, the number of labs and the region in which they'll be deployed. The `plugins_config` section is then used to enable the plugins which we'd like to run in this particular workshop. 

The `{}` are known as maps (or dictionaries). They're used to to pass non-default settings into each of the plugins.

## What does it do?

In a nutshell, the above configuration deploys 50 of the following scenarios:

* **Conformity existing resources:** Participants use [Conformity](https://www.cloudconformity.com/) to identify and close security gaps in deployed resources

* **Conformity template scanner:** Using Conformity's template scanner in a CI/CD pipeline, participants identify and close security gaps in resources **before they're deployed**. Once the template has been secured, the pipeline will automatically deploy the stack

* **Application Security Lambda:** Participants exploit a Lambda function using a directory traversal attack. They then protect it using [Application Security](https://www.trendmicro.com/en_au/business/products/hybrid-cloud/cloud-one-application-security.html) 

* **Application Security Container:** Participants exploit a container using SQL injection and remote code execution (RCE) attacks. They then protect it using Application Security

* **Workload Security Container:** Participants exploit a container using a RCE attack. They then then protect it, and its host using [Workload Security](https://www.trendmicro.com/en_au/business/products/hybrid-cloud/cloud-one-workload-security.html)

* **Container Image Security:** With [Container Image Security](https://www.trendmicro.com/en_au/business/products/hybrid-cloud/cloud-one-container-image-security.html) embedded in their CI/CD pipeline, participants identify and remove vulnerabilities, secrets and malware from a container **before it's deployed**. Once the Dockerfile has been secured, the pipeline allows the image to be put into ECR

## What do the guides look like?

Here's a screenshot from `lab49`'s lab guide:

[![](/assets/2020/08/lab-guide.png)](/assets/2020/08/lab-guide.png)

## Taking it for a spin

We frequently run virtual classrooms and hands on labs around the world. If you'd like to be a hacker for a day and/or learn more about DevSecOps, please feel free to drop me an email. I'll be happy to let you know when our next event is going to take place.

## What's next?

In the subsequent posts of this series, I'll delve deeper into the architecture and inner workings of this solution. 