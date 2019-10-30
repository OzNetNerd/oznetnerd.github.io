---
title: Router URL Filtering using NBAR
sub_heading: "Yet another use for NBAR"
redirect_from: /router-url-filtering-using-nbar/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Security

tags:
- Firewall
- NBAR
- Security
- Cisco
---
There are many ways you can block users from accessing websites they shouldn't be, such as firewalls, proxy servers, DNS servers, etc. However, if you have a small setup, chances are you may not have any of these in place already, and you may be reluctant add another piece of equipment to your network.

This is where your Cisco router can come to the rescue again.

(**Note:** No matter how small your network is, it is highly recommended that you do use firewall(s) to protect your network, whether they come in the form of software installed on each PC, or [**CBAC**](/tags/#cbac) configured on your border router).

Using [**NBAR**](/tags/#nbar) and a policy map, you can have your URL filtering set up in a matter of seconds. Here's an example:

```
class-map match-any BLOCKED_SITES
 match protocol http host "*facebook*"
 match protocol http host "*youtube*"
!
!
policy-map DROP_TRAFFIC
 class BLOCKED_SITES
 drop
!
interface Dialer1
 service-policy output DROP_TRAFFIC
```

The configuration is quite self explanatory.

**Step 1:** You simply create a class-map and use the "match protocol" command to specify the URLs you'd like to block.

(**Note:** You can use [**Regular Expressions**](http://en.wikipedia.org/wiki/Regex#POSIX_Basic_Regular_Expressions) to match the URL. This is what the asterisks (*) mean in the example strings above).

**Step 2:** Create a policy-map that tells the router what to do with traffic that matches the criteria set out by the class-map.

**Step 3:** Apply the policy-map to your Internet facing interface, in the outbound direction.

You can then verify that your configuration is working by issuing the **"sh policy-map interface dialer 1"** command:

```
Router#show policy-map interface dialer 1
 Dialer1

Service-policy output: DROP_TRAFFIC

Class-map: BLOCKED_SITES (match-any)
 36 packets, 44247 bytes
 5 minute offered rate 0 bps, drop rate 0 bps
 Match: protocol http host "*facebook*"
 10 packets, 8391 bytes
 5 minute rate 0 bps
 Match: protocol http host "*youtube*"
 26 packets, 35856 bytes
 5 minute rate 0 bps
 drop
```

Unfortunately, URL filtering is not 100% reliable and can be circumvented quite easily, however, it is a good technique to know nevertheless.

**Note:** Some people feel that using an outbound ACL on your Internet facing interface is sufficient. However, as the ACL statically defines a public IP address, if the website's IP address changes or if the site is load balanced over several IPs, the ACL will not be sufficient. By applying your filter based on the URL, you can be sure that the site will always be blocked so long as it never changes its name.

If they make no sense to you, please re-read my previous posts for more information.