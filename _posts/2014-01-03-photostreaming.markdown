---
layout: single
title: "Streaming Photos from Glass"
date:   2014-01-03
categories: glass
image:
   feature: pictures.png
---

One of the original ideas for Dr. Turner's research was to stream the conductor's perspective from Glass. Due to the limited power of the device, it is currently not possible to live stream without at least a half of a second of latency. To combat this problem, one idea was to take still images and show them to the audience.

There are two Android applications that are necessary to make this task possible: [Tina Time-lapse](https://play.google.com/store/apps/details?id=com.tina.time_lapse) by Wessel Rossing and [FTPServer](https://play.google.com/store/apps/details?id=lutey.FTPServer) by Andreas Liebig. Both of these must be sideloaded onto the device. After installing the applications onto a different rooted Android device, one must navigate to `/data/app/` to find the corresponding APK files. Using ADB's pull function, they can be transferred to a computer before using ADB to install them on Glass.

“Tina Time-lapse” is an Android application that automatically takes pictures at a user-defined time interval. Other settings allow the user to set the image's resolution, quality, and location where the photo is saved. After installing the application using ADB, the image is initially distorted:

<center><img src="/images/pictures.png" alt="distortion" style="width: 600px;"/></center>




This is a [known error](http://stackoverflow.com/questions/19235477/google-glass-preview-image-scrambled-with-new-xe10-release) with the release of EX10. After importing the source code into Eclipse, the addition of one line of Java fixes this problem:

{% highlight java %}
	parameters.setPreviewFpsRange(30000, 30000);
{% endhighlight %}

This is added after the Camera is initialized, in the `Recorder` class in the package `nl.nanatech.b.tinatimelapse`. To begin taking photos, one simply selects the second icon: the camera.

Glass will automatically sleep after several seconds of inactivity. This can be changed by manually installing the `Settings.apk` file, starting the application using ADB, and changing the preference under the Display menu.

Photos can be accessed from a different computer after an FTP Server is configured on Glass. Although there are several applications available to accomplish this task, Andreas Liebig's “FTPServer” has a setting that is required for the device to be network accessible. There are several network interfaces available through Glass:

<table class = "table table-striped">
<tr><td>lo</td><td>UP</td><td>127.0.0.1/8</td><td>0x00000049</td><td>00:00:00:00:00:00</td></tr>
<tr><td>ifb0</td><td>UP</td><td>192.168.167.239/0</td><td>0x000000c3</td><td>86:67:db:e8:XX:XX</td></tr>
<tr><td>ifb1</td><td>DOWN</td><td>0.0.0.0/0</td><td>0x00000082</td><td>3a:a5:94:8c:XX:XX</td></tr>
<tr><td>sit0</td><td>DOWN</td><td>0.0.0.0/0</td><td>0x00000080</td><td>00:00:00:00:00:00</td></tr>
<tr><td>ip6tnl0</td><td>DOWN</td><td>0.0.0.0/0</td><td>0x00000080</td><td>00:00:00:00:00:00</td></tr>
<tr><td>wlan0</td><td>UP</td><td>192.168.1.113/24</td><td>0x00001043</td><td>f8:8f:ca:24:XX:XX</td></tr>
<tr><td></td></tr>
</table>

Only the last interface, `wlan0`, corresponds to the physical MAC address of the device. Due to an issue with the Glass interface, all FTP servers default to use the `ifb0` interface. This prevents other clients from finding Glass on the local area network. The application “FTPServer” allows the interface to be manually set, sidestepping this bug.This error has not yet been reported to the Glass developers. I plan on submitting a bug report after I complete more testing in the coming weeks.

Wessel Rossing provided me the source code for  our research with Glass, but as it is not open source, it will not be posted it on Github. The source for “FTPServer” is also not available online, but no modification to the raw source code is necessary for it to work on Glass.
