---
title: "Lambda packaging the right way"
sub_heading: "Solving the **No module named '_cffi_backend'** error"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps
- AWS
- Architecture

tags:
- DevOps
- AWS

---

## Simplicity

One of the beautiful things about Lambda is its simplicity. You write code, test it and then you deploy it. If it works on your machine, you can guarantee it's going to work in a Lambda function too. If you want to use 3rd party libraries, you can package them alongside your code, or use Layers. It really is that simple.

## Problem

At times though, it doesn't feel that simple. For example, a [quick Google search](https://www.google.com/search?q=_cffi_backend+lambda) returns numerous results where devs encounter errors such as:

{% highlight json linenos %}
{
  "errorMessage": "Unable to import module 'python_handler': No module named '_cffi_backend'",
  "errorType": "Runtime.ImportModuleError"
}
{% endhighlight %}

The strange thing is, they appear to be doing everything right. The code works on their machine, they've packaged it up correctly and deployed it successfully. Why then are they encountering issues? Looking at the search results, there's no clear answer. Fixes range from uninstalling and re-installing packages, downgrading Python and/or `cffi` and finally, [running the code on EC2](https://stackoverflow.com/a/57221052/6233477). Given that this is the "Accepted" answer, it's safe to assume it resolved the person's issue. 

Though it might not seem it, this is actually the hint we needed to solve the problem. You see, some Python libraries have Operating System level dependencies. Because we packaged up the Lambda and its dependencies on our machine, the libraries have been set up for our OS. Given that the Lambda is likely using a different OS to us, it explains why our function doesn't work. 

Taking this hypothesis further, it is conceivable that the Lambda is running the same OS as Amazon Linux 2. This explains why others have reported packaging the code up on EC2 and then uploading it to Lambda resolves the issue.

## Solution  

Though the Amazon Linux 2 approach solves our problem, it feels very hacky and would be very time consuming. Thankfully, there's a better option. And [here it is!](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-layer-simulated-docker/) Using this method, we get the best of both worlds. We can develop and test our code locally and then push Lambda-compatible code to AWS.

The below example is taken from my ["Application Security" demo](https://github.com/OzNetNerd/Cloud-One-Application-Security-Serverless#lambda-layer---linuxmacwindows) and shows how this container can be used to create a Lambda layer:

{% highlight bash linenos %}
# prepare directory & requirements file
cd /tmp 
mkdir -p app-sec-layer/python/lib/python3.7/
cd app-sec-layer
echo "trend-app-protect" >> requirements.txt

# use container to produce Lambda-compatible libraries
docker run \
-v "$PWD":/var/task \
"lambci/lambda:build-python3.7" \
/bin/sh -c "pip install -r requirements.txt -t python/lib/python3.7/site-packages/; exit"

# zip the libraries so that they
# can be uploaded as a layer
zip -r trend.zip python > /dev/null

# create a Lambda layer so that the 
# libraries can be used by multiple Lambdas 
aws lambda publish-layer-version \
--layer-name app-protect \
--description "Trend Micro Application Security" \
--zip-file fileb://trend.zip \
--compatible-runtime "python3.7"
{% endhighlight %}

And just like that, our code is ready to run!