---
title: Fast GNS3 Router Bootup
sub_heading: "Turning on the GNS3 afterburners!"
redirect_from: /fast-gns3-router-bootup/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs

tags:
- GNS3
- Labs
- Cisco
---
As all Cisco IOS users/Cisco engineers will have seen when booting up a switch or a router, the device will decompress the image file which is represented by a series of # signs that flood across the screen. While it does not take an extremely long time to decompress the image, it can seem like it is taking an eternity if your wanting to get a lab up and running so you can test something quickly. Thankfully, [**GNS3**](/tags/#GNS3) allows you to skip the decompression process by decompressing the image before hand.

All you need to do is open your IOS' .bin file with WinRar. When you do this, you will see another .bin file. Now all you need to do is extract this "new" .bin file and either rename it, or leave it as is. If you decide to do the former you will need to delete the original .bin file (that you opened with WinRar) and give the new .bin the original's name. (Note: The deletion is necessary as you cannot have two files with the same name in the same directory). If you decide to do the latter, you will need to update your GNS3 configuration so that it uses the new image when booting your virtual routers.

If you haven't already guessed, the original .bin is actually a compressed image and the "new" .bin is the decompressed version of that same image. What WinRar does is decompresses the image so that GNS3 doesn't have to. This can save a lot of time, especially if you use GNS3 regularly.

Note: It is interesting to see the size difference between the compressed (original) and decompressed ("new") images. In my experience I have found more often than not the original image is almost half the size of the "new" image. It may be worth keeping this in mind, especially if you are running low on hard drive space.