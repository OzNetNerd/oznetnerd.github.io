---
title: EIGRP Network Command Tips
sub_heading: "Tips to improve your EIGRP game"
redirect_from: /eigrp-network-command-tips/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- EIGRP

tags:
- GNS3
- Labs
- Cisco
- EIGRP
- Routing Protocols
- Auto Summary
---
I discussed the EIGRP **"network"** command in the [**EIGEP Network Command – The Easy Way Out**](/eigep-network-command-easy-way/ "EIGEP Network Command – The Easy Way Out") post, as well as the [**EIGRP Route Advertising**](/eigrp-route-advertising/ "EIGRP Route Advertising") post, and I'm about to discuss it again in this post. Who knew one command would be the source of so many posts! :)

If given incomplete information, the EIGRP **"network"** command will do its best to help you complete the command, however, unfortunately it doesn't always guess correctly.

For example, if you wanted to activate EIGRP on a single interface on your router which had the IP address **172.19.65.22**, you'd use the following commands:

```
R3(config-router)#router ei 10
R3(config-router)#network 172.19.65.22 0.0.0.0
```

You could then use the "do" command [**which I discussed previously**](/command/ "The “do” Command") to verify that the router took your configuration entry correctly, as per the below:

```R3(config-router)#do sh run | begin router eigrp
router eigrp 10
 network 172.19.65.22 0.0.0.0
```

However, if you were to accidentally forget to add the wildcard mask to the end of the command and you instead entered:

```
R3(config-router)#router ei 10
R3(config-router)#network 172.19.65.22
```

The router would, incorrectly, take a guess at what you really meant to type, as per the output below:

```
R3(config-router)#do sh run | begin router eigrp
router eigrp 10
 network 172.19.0.0
```

As you did not specify a wildcard mask, the router identified the IP as a Class B address and therefore only kept the Class B portion of your entry. This is why you need to be very careful when using the **"network"** command and why it is also a good idea to double check your configuration before closing your session.

**Note:** A similar thing occurs when you do not use the [**no auto-summary**](/eigrp-no-auto-summary-command-part-1/ "EIGRP No Auto Summary Command, Part 1") command.

The next EIGRP **"network"** command topic I'd like to discuss is the use of subnet masks. Throughout all of my [**EIGRP posts**](/tags/#eigrp), I have been using wildcard masks in my configuration examples as this is what EIGRP uses to determine which networks and interfaces it should advertise or use. However, you can actually use a subnet mask instead of a wildcard mask and the IOS will automatically convert it to a wildcard mask and add it to the running configuration. Here is an example:

```
R3(config-router)# network 10.16.0.0 255.255.128.0
R3(config-router)#do sh run | begin router eigrp
router eigrp 10
 network 10.16.0.0 0.0.127.255
```

As some might find this an easy way of doing things, I recommend manually calculating the wildcard mask yourself as it is not only good practice but may be required for exams that you sit in the future**.**