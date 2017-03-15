---
layout: single
title: "Score Viewer 2.0 for Glass"
date:   2014-06-01
categories: glass
header:
  image: /assets/images/edward.jpg
  credit: Andre, Cindi, and I all reading music notation from Glass.
---

Last November, we finished the [first version](https://github.com/tehrlich/scoreviewer) of the music score viewing application. Built on the [Mirror API](https://developers.google.com/glass/develop/mirror/index), the app pushed a series of cards (what we had as the score of *Armenian Dances* by Aram Khachaturian) to Glass. Although functional, the app had a few problems.

1. Google App Engine (GAE) would occasionally timeout while sending cards to Glass. I didn't solve this issue fully, but from what I could tell, since we were sending many calls to Glass, the program would stall waiting for a response and terminate. This was far from ideal, and even at GAE's highest timeout threshold it would still stall on slow applications. A possible fix for this would be to have a different thread responsible for sending the cards, and a loading screen telling the user to wait. Since I knew we were going to start from scratch on this next version, I never implemented this.

2. The original app sent all different cards, versus stacked in one bundle. If the user received an email or text message while the bundle of score pages was being pushed to his or her Glass, that would be inserted in the middle of the score. Awkward. I was able to fix this by bundling the cards in one stack.

3. If you received new cards while performing, they would take precedence over your score. Turning off wifi and Bluetooth fixed this, but that's not truly an ideal solution.

4. Controlling the cards was limited to the gestures on the touchpad or through a Bluetooth controller. We used a wireless keyboard to advance pages, but pairing the device was awkward and required a hack to work.

For these reasons, I immediately started using the [Glass Development Kit (GDK)](https://developers.google.com/glass/develop/gdk/) to write the next version of the score viewer. Setting up the programming environment for Glass is pretty straightforward and is discussed in the documentation for the GDK.

At its core, the application is very straightforward. An [ImageView](http://developer.android.com/reference/android/widget/ImageView.html) loads up the first page of a score, which is simply a 640x360px image. After a certain event occurs, the next image is loaded. After all are cycled through, the score returns to the first page.

Here is the cool feature: to advance pages, the user blinks twice while looking at the Glass display. To go to a previous page, the user taps the touchpad once. This decision was a conscious one; tapping the touchpad is faster than swiping back and forth. In order to get this functionality to work, I used Takahiro "Poly" Horikawa's Eye Gesture Library on Github, available [here](http://github.com/thorikawa/EyeGestureLib).

So, how well does it work? Truthfully, quite well. After getting used to looking through or away from Glass, I'm always successful in having the pages advance when they should. Occasionally a "phantom blink" happens, and the page advances before it should. A quick tap brings it back in place. This could be disastrous in live performance, especially if musicians are reading notation off of the device, versus having a conductor use it as a reminder of meter changes and cues. I've found that calibrating the winking sensor can greatly reduce phantom blinks. Also, be sure the "wink for photo" setting is enabled in the settings menu before using the app.

Although a bit messy, the source for the Glass Score Viewer 2.0 is available on Github below. The demo score installed is Khachaturian's *Armenian Dances*. I hope to comment some more of the code and upload a revised version soon.

[https://github.com/tehrlich/gdkscoreviewer](https://github.com/tehrlich/gdkscoreviewer)
