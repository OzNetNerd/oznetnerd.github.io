---
title: Getting started with Ansible
sub_heading: "Spoiler alert: It's easier than you think"
redirect_from: /getting-started-with-with-ansible/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible
- Python

tags:
- Ansible
- Automation
- Python
- DevOps
---
In my previous post, [**Getting started with Network Automation**](/getting-started-network-automation/), I discussed why I chose Ansible as my configuration management and orchestration tool over the other options.

In this post I'm going to discuss the the hurdles I encountered after making this decision, and of course the solutions too.

## Which technique should I use?

The internet is an amazing place. It is a massive wealth of knowledge that can help you learn about any topic you could ever dream of because people have been sharing their knowledge for years through things like blog and forum posts. While this is very much a blessing, it can also be seen as a minor curse too.

When studying a new topic, an issue some may experience is encountering old information and thinking it is still valid. Or perhaps it is still valid, but it might not be the best way of doing things anymore.

For example, when I started looking into how to get Ansible to interact with Cisco lab, I found several options:

*   [**SNMP**](http://packetpushers.net/ansible-cisco-snmp/)
*   [**Netmiko + SCP**](https://pynet.twb-tech.com/blog/automation/cisco-ios.html)
*   **[netlib Python library](https://www.packetgeek.net/2015/08/using-ansible-to-push-cisco-ios-configurations/)**
*   [**Paramiko Python library**](http://networkop.co.uk/blog/2015/06/24/ansible-intro/)
*   [**NTC-Ansible**](https://projectme10.wordpress.com/2015/11/03/enabling-network-automation-using-ntc-ansible/)
*   [**ios_template**](https://docs.ansible.com/ansible/ios_template_module.html)
*   [**ios_command**](https://docs.ansible.com/ansible/ios_command_module.html)
*   [**ios_config**](https://docs.ansible.com/ansible/ios_config_module.html)

To say this is was a little overwhelming would be an understatement. Having said that though, I love a challenge and I love learning new things, so I made it my mission to sort through these options.

As the saying goes, there is more than one way to skin a cat. That's why we see a few Python libraries, SCP and SNMP being mentioned in the techniques listed above. Having said this though, why would I want to use any of these techniques when the last three options are Ansible Core modules?

Well, if we take a look at the datestamps of the other links we can see that they were released before the Core modules and therefore they were the only options at that time. Further to this, these options are still very valid for vendors' equipment which do not have Ansible modules written for them.

(Note that the  **ios_template** module has been deprecated in Ansible 2.2 and **ios_config** should be used instead.)

(Also note that NTC-Ansible is a fantastic tool which serves other purposes and I will therefore cover it in a future post.)

## Conclusion

There are a couple of messages I hope I conveyed in this blog post;

It is OK to feel overwhelmed. All you need to do is persevere and you will get there.. At the end of the day studying wouldn't be fun if it weren't challenging.

Also, while the internet is full of helpful people sharing their knowledge through things like blog posts, forums, videos, etc, this information eventually becomes outdated. Even this very blog post will become become outdated too. If you find yourself becoming confused as there are multiple ways to do a single task, check the vendor's latest documentation to see what their best practices are. If you're still unsure though, you can contact the vendor directly, jump onto a forum, Slack, IRC or even contact the blogger(s) to see which is the current standard way to achieve that task.