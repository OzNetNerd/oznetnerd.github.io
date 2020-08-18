---
title: "Python: Demystifying AWS' Boto3"
sub_heading: "The best thing since sliced bread"
redirect_from: /python-demystifying-aws-boto3/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Python
- Boto3
- DevOps
- AWS

tags:
- Python
- AWS
- Amazon
- IaC
- Boto3
- DevOps
---
As the [**GitHub page**](https://github.com/boto/boto3) says, _"Boto3 is the Amazon Web Services (AWS) Software Development Kit (SDK) for Python, which allows Python developers to write software that makes use of services like Amazon S3 and Amazon EC2."_

The good news is that Boto 3 is extremely [**well documented.**](https://boto3.readthedocs.io/en/latest/) However, the bad news is that it is quite difficult to follow. The documentation starts with a [**Quickstart guide**](https://boto3.readthedocs.io/en/latest/guide/quickstart.html), followed by a [**Sample Tutorial**](https://boto3.readthedocs.io/en/latest/guide/sqs.html) followed then by [**Code Examples**](https://boto3.readthedocs.io/en/latest/guide/examples.html). This is all good stuff, though it doesn't give you much of an understanding of how to actually use Boto 3. For example,  we see things such as:

```python
# Create CloudWatch client
cloudwatch = boto3.client('cloudwatch')
```

and,

```python
# Get the service resource
sqs = boto3.resource('sqs')
```

But we haven't yet learned what a [**client**](http://boto3.readthedocs.io/en/latest/guide/clients.html) and a [**resource**](http://boto3.readthedocs.io/en/latest/guide/resources.html) is, nor do we see [**sessions**](http://boto3.readthedocs.io/en/latest/guide/session.html) mentioned until much later in the documentation. But I digress. Let's go ahead and get started!

## 10,000 foot view

The [**What's New page**](https://boto3.readthedocs.io/en/latest/guide/new.html) gives you a great, high level view of the parts which make up Boto 3.

_Boto 3 consists of the following major features:_

*   _**Resources:** a high level, object oriented interface_
*   _**Collections:** a tool to iterate and manipulate groups of resources_
*   _**Clients:** low level service connections_
*   _**Paginators:** automatic paging of responses_
*   _**Waiters:** a way to block until a certain state has been reached_

_Along with these major features, Boto 3 also provides [**sessions**](http://boto3.readthedocs.io/en/latest/guide/session.html) and per-session credentials & configuration, as well as basic components like authentication, parameter & response handling, an event system for customizations and logic to retry failed requests._

**Note:** You can have a read of the [**Resources page**](http://boto3.readthedocs.io/en/latest/guide/resources.html) for more information, though I'll expand on the above later on in this post too.

## Botocore

_Boto 3 is built atop of a library called Botocore, which is shared by the AWS CLI. Botocore provides the **low level clients, session, and credential & configuration data.** Boto 3 builds on top of Botocore by providing its own session, resources and collections._

The [**Migration page**](https://boto3.readthedocs.io/en/latest/guide/migration.html), while aimed at users who are going from Boto 2.X to Boto 3, is actually quite useful for newcomers too, as per the following points:

*   _Modules are typically split into two categories, those which include a **high-level object-oriented** interface and those which include only a **low-level interface** which matches the underlying **Amazon Web Services API.**_
    *   **Note:** These interfaces are [**resources**](http://boto3.readthedocs.io/en/latest/guide/resources.html) and [**clients**](http://boto3.readthedocs.io/en/latest/guide/clients.html) respectively. We'll take a look at both of them shortly.
*   _Some modules are **completely high-level** (like Amazon S3 or EC2), some include **high-level** code on top of a **low-level** connection (like Amazon DynamoDB), and others are **100% low-level** (like Amazon Elastic Transcoder)._

## Credentials

Almost all of the examples I've come across in the documentation are similar to what was shown above (and pasted again below for ease of reference), where a `client` or `resource` specifies an AWS service and nothing else:

```
# Create CloudWatch client
cloudwatch = boto3.client('cloudwatch')
```

However, when you try it yourself, you'll likely get quite a lot of errored output:

```python
In [1]: import boto3

In [2]: cloudwatch = boto3.client('cloudwatch')
---------------------------------------------------------------------------
NoRegionError                             Traceback (most recent call last)
 in ()
----> 1 cloudwatch = boto3.client('cloudwatch')

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\boto3\__init__.py in client(*args, **kwargs)
     81     See :py:meth:`boto3.session.Session.client`.
     82     """
---> 83     return _get_default_session().client(*args, **kwargs)
     84
     85

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\boto3\session.py in client(self, service_name, region_name, api_version, use_ssl, verify, endpoint_url, aws_access_key_id, aws_secret_access_key, aws_session_token, config)
    261             aws_access_key_id=aws_access_key_id,
    262             aws_secret_access_key=aws_secret_access_key,
--> 263             aws_session_token=aws_session_token, config=config)
    264
    265     def resource(self, service_name, region_name=None, api_version=None,

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\session.py in create_client(self, service_name, region_name, api_version, use_ssl, verify, endpoint_url, aws_access_key_id, aws_secret_access_key, aws_session_token, config)
    834             is_secure=use_ssl, endpoint_url=endpoint_url, verify=verify,
    835             credentials=credentials, scoped_config=self.get_scoped_config(),
--> 836             client_config=config, api_version=api_version)
    837         return client
    838

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\client.py in create_client(self, service_name, region_name, is_secure, endpoint_url, verify, credentials, scoped_config, api_version, client_config)
     69         client_args = self._get_client_args(
     70             service_model, region_name, is_secure, endpoint_url,
---> 71             verify, credentials, scoped_config, client_config, endpoint_bridge)
     72         service_client = cls(**client_args)
     73         self._register_retries(service_client)

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\client.py in _get_client_args(self, service_model, region_name, is_secure, endpoint_url, verify, credentials, scoped_config, client_config, endpoint_bridge)
    281         return args_creator.get_client_args(
    282             service_model, region_name, is_secure, endpoint_url,
--> 283             verify, credentials, scoped_config, client_config, endpoint_bridge)
    284
    285     def _create_methods(self, service_model):

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\args.py in get_client_args(self, service_model, region_name, is_secure, endpoint_url, verify, credentials, scoped_config, client_config, endpoint_bridge)
     43         final_args = self.compute_client_args(
     44             service_model, client_config, endpoint_bridge, region_name,
---> 45             endpoint_url, is_secure, scoped_config)
     46
     47         service_name = final_args['service_name']

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\args.py in compute_client_args(self, service_model, client_config, endpoint_bridge, region_name, endpoint_url, is_secure, scoped_config)
    109
    110         endpoint_config = endpoint_bridge.resolve(
--> 111             service_name, region_name, endpoint_url, is_secure)
    112
    113         # Override the user agent if specified in the client config.

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\client.py in resolve(self, service_name, region_name, endpoint_url, is_secure)
    354         region_name = self._check_default_region(service_name, region_name)
    355         resolved = self.endpoint_resolver.construct_endpoint(
--> 356             service_name, region_name)
    357         if resolved:
    358             return self._create_endpoint(

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\regions.py in construct_endpoint(self, service_name, region_name)
    120         for partition in self._endpoint_data['partitions']:
    121             result = self._endpoint_for_partition(
--> 122                 partition, service_name, region_name)
    123             if result:
    124                 return result

c:\users\will\appdata\local\programs\python\python36-32\lib\site-packages\botocore\regions.py in _endpoint_for_partition(self, partition, service_name, region_name)
    133                 region_name = service_data['partitionEndpoint']
    134             else:
--> 135                 raise NoRegionError()
    136         # Attempt to resolve the exact region for this partition.
    137         if region_name in service_data['endpoints']:

NoRegionError: You must specify a region.
``` 

The good news is that the output is easy to deciphered. In fact, the last line of the output gives us a pretty big hint: `NoRegionError: You must specify a region.`

After some searching I came across the [**Session Reference page**](http://boto3.readthedocs.io/en/latest/reference/core/session.html), and it was exactly what I was looking for. It tells us the parameters which can be specified for both [**sessions**](http://boto3.readthedocs.io/en/latest/guide/session.html) (which can be used for **clients** and **resources**):

```python
class boto3.session.Session(aws_access_key_id=None, aws_secret_access_key=None, aws_session_token=None, region_name=None, botocore_session=None, profile_name=None)
```

as well as [**clients:**](http://boto3.readthedocs.io/en/latest/guide/clients.html)

```python
client(service_name, region_name=None, api_version=None, use_ssl=True, verify=None, endpoint_url=None, aws_access_key_id=None, aws_secret_access_key=None, aws_session_token=None, config=None)
```

and [**resources**](http://boto3.readthedocs.io/en/latest/guide/resources.html):

```python
resource(service_name, region_name=None, api_version=None, use_ssl=True, verify=None, endpoint_url=None, aws_access_key_id=None, aws_secret_access_key=None, aws_session_token=None, config=None)
```

I then found the [**Configuring Credentials**](https://boto3.readthedocs.io/en/latest/guide/configuration.html) section of the documentation. With these two pieces of information, I was able to put together a working solutions for **sessions**:

```python
import boto3

aws_access_key_id = ''
aws_secret_access_key = ''
region_name = 'ap-southeast-2'

session = boto3.session.Session(aws_access_key_id=aws_access_key_id,
                                aws_secret_access_key=aws_secret_access_key,
                                region_name=region_name)

ec2 = session.resource('ec2')
``` 

as well as for **clients**:

```python
import boto3

aws_access_key_id = ''
aws_secret_access_key = ''
region_name = 'ap-southeast-2'

ec2 = boto3.client('ec2',
                   aws_access_key_id=aws_access_key_id,
                   aws_secret_access_key=aws_secret_access_key,
                   region_name=region_name)
``` 

and **resources**:

```python
import boto3

aws_access_key_id = ''
aws_secret_access_key = ''
region_name = 'ap-southeast-2'

ec2 = boto3.resource('ec2',
                   aws_access_key_id=aws_access_key_id,
                   aws_secret_access_key=aws_secret_access_key,
                   region_name=region_name)
``` 

_**Note:**__ As you can see, the code extracts above specify the Key ID and Secret Key inside the script itself. This was done to help newcomers get up and running with minimal fuss. However, it is not advisable to use this method permanently nor in an production environment._

## Sessions

The [**Sessions documentation**](http://boto3.readthedocs.io/en/latest/guide/session.html) is a little cryptic in regards to when you should use them, saying: _"It is possible and recommended to maintain your own session(s) in some scenarios." _Unfortunately it doesn't go on to explain which scenarios.

Having said that, heading back over to the [**Session Reference page**](http://boto3.readthedocs.io/en/latest/reference/core/session.html) we see that _"A session stores configuration state and allows you to create service clients and resources."_ so that gives us a pretty good idea on what they're used for.

If you'd like additional input on Sessions, see [**this StackOverflow post**](https://stackoverflow.com/a/42818143/6233477).

## Services documentation

The [**Services documentation**](http://boto3.readthedocs.io/en/latest/reference/services/index.html) provides information on how to use Clients and Resources (where available) for each of AWS' services. It's important to note that while each service has its own page of documentation, the Client and Resource sections of these documents are not clearly defined.

For example, heading over to the [**EC2 documentation**](http://boto3.readthedocs.io/en/latest/reference/services/ec2.html) we see the following:

*   Client
*   Paginators
*   Waiters
*   Service Resource
*   ClassicAddress
*   DhcpOptions
*   Image
*   Instance

Because of the flat hierarchy of Boto 3's Table of Contents, newcomers might not realise that only the Client, Paginators and Waiters sections pertain to **Client** configuration, while all other sections pertain to **Resources**.

## Clients

The [**Low-level Clients page**](http://boto3.readthedocs.io/en/latest/guide/clients.html) tells us that:

*   Clients support all services.
*   **The outputs are returned using Python dictionaries.**
*   **We have to traverse these dictionaries ourselves.**
*   We need to keep in mind that _"responses may not always include all of the expected data."_

Let's take a look at the two points I've highlighted in bold. But first, let's use the EC2 Client (as well as a Session) to create an instance:

```python
import boto3

aws_access_key_id = ''
aws_secret_access_key = ''
region_name = 'ap-southeast-2'

session = boto3.session.Session(aws_access_key_id=aws_access_key_id,
                                aws_secret_access_key=aws_secret_access_key,
                                region_name=region_name)

ec2client = session.client('ec2')

client_instance = ec2client.run_instances(
    ImageId='ami-30041c53',
    KeyName='Keys',
    MinCount=1,
    MaxCount=1,
    InstanceType='t2.micro')
``` 

The [**run_instances**](http://boto3.readthedocs.io/en/latest/reference/services/ec2.html#EC2.Client.run_instances) documentation gives us a large amount of useful information. For example:

*   **Request Syntax: **How we can customise our instance under the section.
*   **Response Syntax**: What output we can expect to see.
*   **Return type**: The format in which the output will be provided. _(Recall that Clients will always have a return type of dict. Resources on the other hand will have varying return types.)_

Let's now check the contents of `client_instance`:

```python
In [13]: client_instance
Out[13]:
{'Groups': [],
 'Instances': [{'AmiLaunchIndex': 0,
   'Architecture': 'x86_64',
   'BlockDeviceMappings': [],
   'ClientToken': '',
   'EbsOptimized': False,
   'Hypervisor': 'xen',
   'ImageId': 'ami-30041c53',
   'InstanceId': 'i-0e3a125b86073f341',

# omitted
```

As we suspected, it is a dictionary.

Now let's cover off what the documentation means when it says we have to traverse the output ourselves. Say we want to obtain the `InstanceId` from the `client_instance` dictionary. We would need traverse it to the point where the instance ID resides:

```python
In [17]: client_instance['Instances'][0]['InstanceId']
Out[17]: 'i-0e3a125b86073f341'
```

_For more informaiton on dictionary traversal, please see the [**Understanding Ansible Output Structure**](/understanding-ansible-output-structure/) post._

**Note:** There are other ways to access the InstanceId, such as using [**describe_instance_status()**](http://boto3.readthedocs.io/en/latest/reference/services/ec2.html#EC2.Client.describe_instance_status) as per the output below. However, this does not negate the need to traverse the dictionary. Needless to say, it doesn't feel all that Pythonic!

```python
In [23]: ec2client.describe_instance_status()
Out[23]:
{'InstanceStatuses': [{'AvailabilityZone': 'ap-southeast-2b',
   'InstanceId': 'i-0e3a125b86073f341',
   'InstanceState': {'Code': 16, 'Name': 'running'},
   'InstanceStatus': {'Details': [{'Name': 'reachability',
      'Status': 'passed'}],

# Omitted
```

## Resources

The [**Resources documentation**](http://boto3.readthedocs.io/en/latest/guide/resources.html) tells us that _"Resources represent an object-oriented interface to Amazon Web Services (AWS). They provide a higher-level abstraction than the raw, low-level calls made by service clients."_

In other words, Resources (where available), can do the same thing as Clients but produce outputs which are a lot easier to consume, once we get the hang of it.

The documentation then goes on to tell us that _"These can conceptually be split up into identifiers, attributes, actions, references, sub-resources, and collections"_. Let's go ahead and look into what these are.

### Identifiers, attributes, references, actions & collections

Where Clients are fairly easy to wrap your head around, there's quite a lot more to Resources. Thankfully AWS' documentation gives us a great starting point. The italicised points below is AWS' take on things, while the non-italicised points are my comments:

*   **Identifiers:**
    *   _Properties of a resource that are set upon instantation of the resource._
    *   When a resource is created, it is given an ID. This ID can then be used in subsequent Attribute and Action calls.
*   **Attributes:**
    *   _Provide access to the properties of a resource. Attributes are lazy-loaded the first time one is accessed via the load() method._
    *   Used in conjunction with a resource's ID, Attributes provide information about the specified resource. For example, passing an [**EC2 instance's ID**](https://boto3.readthedocs.io/en/latest/reference/services/ec2.html#instance) to the `image_id` attribute shows us the AMI that this instance is running:
        
        ```python
        In [61]: ec2resource.Instance('i-0afb49cac524f3191').image_id
        Out[61]: 'ami-30041c53'
        ```     
*   **References:**
    *   _Related resource instances that have a belongs-to relationship._
    *   While attributes require a resource's ID, References are methods that belong to the Python instance's object and therefore do not need the ID to be provided:
        
        In [65]: resource_instance[0].vpc.id
        Out[65]: 'vpc-9e22dcfb
        
*   **Actions:**
    *   _Call operations on resources. They may automatically handle the passing in of arguments set from identifiers and some attributes. _
    *   Make something happen. For example, shut down an instance:
        
        ```python
        In [69]: resource_instance[0].stop()
        Out[69]:
        {'ResponseMetadata': {'HTTPHeaders': {'content-type': 'text/xml;charset=UTF-8',
           'date': 'Thu, 31 Aug 2017 11:45:08 GMT',
           'server': 'AmazonEC2',
           'transfer-encoding': 'chunked',
           'vary': 'Accept-Encoding'},
          'HTTPStatusCode': 200,
        
        # omitted
        ````
        
*   **Collections:**
    *   _Provide an interface to iterate over and manipulate groups of resources._
    *   Outputs which need to be iterated over, for example the IP addresses in a VPC or the number of volumes attached to an instance:
        
        ```python
        for volume in resource_instance[0].volumes.all():
            print(volume.id)
        
        vol-0e6630fdcea489f65
        ```
        
*   **Waiters:**
    *   _Provide an interface to wait for a resource to reach a specific state._
    *   For more information, see the [**Resources page**](https://boto3.readthedocs.io/en/latest/guide/resources.html).

### EC2 instance using a Resource

Let's now go ahead and use a session to create an EC2 Resource:

```python
import boto3

aws_access_key_id = ''
aws_secret_access_key = ''
region_name = 'ap-southeast-2'

session = boto3.session.Session(aws_access_key_id=aws_access_key_id,
                                aws_secret_access_key=aws_secret_access_key,
                                region_name=region_name)

ec2resource = session.resource('ec2')

resource_instance = ec2resource.create_instances(
    ImageId='ami-30041c53',
    KeyName='Keys',
    MinCount=1,
    MaxCount=1,
    InstanceType='t2.micro')
``` 

Those with a keen eye for detail may have noticed that the configuration Client and Resource configuration is almost identical. The only differences being the following two bits:

*   `session.resource` as opposed to `session.client`
*   `ec2resource.create_instances` as opposed to `ec2client.run_instances`

However, that's where the similarities end. For example, when we `resource_instance`  we see a fraction of the information we saw when we did the same with `client_instance` :

```python
In [11]: resource_instance
Out[11]: [ec2.Instance(id='i-0afb49cac524f3191')]
```

Wow, what a difference. The Client output a large amount of details about the instance whereas the Resource only shows us the instance's ID.  As a result of this, we'll need to extract the ID to obtain more information on the instance itself.

To extract the this ID we use a similar technique to the one we used when extracting the ID from `client_instance`. Though while in that case we traverse a dictionary, this time we're navigating an OOP instance:

```python
In [12]: resource_instance[0].id
Out[12]: 'i-0afb49cac524f3191'
```

## Congratulations!

If you've made it to the end of this post, you most certainly deserve a pat on the back. I didn't intend for this to be such a length post, but once I got started I couldn't stop :)

I hope you enjoyed it. Please feel free to drop me a message if you'd like me to expand on any part or parts of Boto 3 and I'll be happy to oblige.