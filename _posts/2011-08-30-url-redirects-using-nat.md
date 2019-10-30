---
title: URL Redirects using NAT
sub_heading: "Another interesting use for NBAR"
redirect_from: /url-redirects-using-nat/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Misc.
- NAT

tags:
- NBAR
- Cisco
- NAT
---
In my previous post, [**Router URL Filtering using NBAR**](/router-url-filtering-using-nbar/ "Router URL Filtering using NBAR"), I explained how it was possible to block users from accessing websites simply by using [**NBAR**](/tags/#nbar), a class-map and a policy-map.

In this post I'll describe how you can redirect your users' web requests instead of simply blocking them. This time we'll use [**NAT**](/tags/#NAT) instead of NBAR.

For this example, let's say you'd prefer everyone on your network to use Google instead of Yahoo, so every time someone goes to Yahoo.com, they'll be re directed to Google.com.au

To do this, you'll need to obtain the web server IP addresses for both Yahoo and Google. This can be done easily enough with a ping:

```
Pinging yahoo.com [98.137.149.56] with 32 bytes of data:
Pinging google.com.au [74.125.237.51] with 32 bytes of data:
```

Now all we'll need to do is put these two IP addresses in to a single NAT entry and we're done, like so:

```
ip nat outside source static 98.137.149.56 74.125.237.51
```

As mentioned in **[previous post](/router-url-filtering-using-nbar/ "Router URL Filtering using NBAR")** however, using IP addresses instead of domain names is not ideal as large websites such as Google, YouTube, etc, use multiple IP addresses for their websites and therefore the above method will not get you a 100% success rate.