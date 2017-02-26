---
layout: single
title: "BeatKeeper Version 1.0"
date:   2013-11-06
categories: glass
---
<i>Reposted from the CU Winds Google Glass blog available <a href="http://blogs.cornell.edu/cuwindsglass/2013/11/06/beatkeeper-version-1-0/">here</a>.</i>

<p>Cindi and I spent a few hours two weeks ago getting BeatKeeper up and running on Glass. After cloning the repository on my local machine, I imported the app into Eclipse and pushed it to the device. We were immediately presented with something that looks like this:</p>

<center><p><a href="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper1-mxu0ki.png"><img class="alignnone size-full wp-image-240" title="beatkeeper1" src="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper1-mxu0ki.png" alt="" width="640" height="360" /></a></p></center>

<p>It was quite exciting for us to see a metronome! I swiped forward and back to navigate through the controls; I hit play and a consistent pulse started in my ear. Perfect.</p>

<p>Next thing was making it landscape and more easy to read. I headed into the <code>AndroidManifest.xml</code> file and changed <code>android:screenOrientation="portrait"</code> to <code>android:screenOrientation="landscape"</code>. Now we were presented with something a bit more usable, but the Start button was cut off:</p>

<center><p><a href="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper2-16qrdna.png"><img class="alignnone size-full wp-image-237" title="beatkeeper2" src="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper2-16qrdna.png" alt="" width="640" height="360" /></a></p></center>

<p>This was a great time for me to introduce layout files to Cindi, so I went over the XML and explained how the placement worked. We messed around with the layout and sizing and ended up here:</p>

<center><p><a href="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper-2lle8xw.png"><img class="alignnone size-full wp-image-235" title="beatkeeper" src="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper-2lle8xw.png" alt="" width="640" height="360" /></a></p></center>

<p>After our NYC media lunch, I put in a lot of thought about usability. Buttons are appropriate for mobile devices, but not really for Glass. Using the slider and tap gestures are considerably more intuitive and simple. In their <a href="https://developers.google.com/glass/guidelines">usability guidelines</a>, Google writes, &#8220;Glass users expect the technology to be there when they want it and out of the way when they don&#8217;t. Your Glassware should never take precedence over users&#8217; lives.&#8221; Especially in a rehearsal setting, I wanted to design something simple and intuitive.</p>

<p>Here is the first real version of BeatKeeper for Glass:</p>

<center><p><a href="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper3-1kzxr8q.png"><img class="alignnone size-full wp-image-241" title="beatkeeper3" src="http://blogs.cornell.edu/cuwindsglass/files/2013/11/beatkeeper3-1kzxr8q.png" alt="" width="640" height="360" /></a></p></center>

<p>It&#8217;s simple and easy to read. To adjust the tempo, simply slide your finger on the side of Glass. To start and start the metronome, tap once. All of the gestures are recognized via a <a href="http://developer.android.com/reference/android/view/GestureDetector.html"><code>GestureDetector</code></a>. I&#8217;ve stripped all unnecessary controls from the app. The resulting experience is sleek and straight forward to use.</p>

Here is the source for the `GestureDetector`.
{% highlight java %}
public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX,
					   float distanceY) {
    Log.d("Gesture Example", "onScroll: distanceX:" + distanceX +
    " distanceY:" + distanceY);
    if (distanceX < -1) { //determine scrolling direction
    	bpm++;
    	TextView bpmText = (TextView) findViewById(R.id.bps);
        bpmText.setText(""+bpm);
        metroTask.setBpm(bpm);
    } else if (distanceX > 1) { 
    	bpm--;
    	TextView bpmText = (TextView) findViewById(R.id.bps);
        bpmText.setText(""+bpm);
        metroTask.setBpm(bpm);
    } return true;
{% endhighlight %}

<p><strong><span style="line-height: 1.714285714; font-size: 1rem;">So what&#8217;s next for BeatKeeper for Glass?</span></strong></p>

<p>A reverse metronome is a tool for finding a tempo via tapping. Over the next few days, I hope to create another simple tool that averages taps on the device. I&#8217;m not sure if it will be a standalone application or integrated with BeatKeeper. Although the two go hand-in-hand, it would be difficult to switch functions between a metronome and a reverse metronome. A long press on the side could change the mode of the application, but if you press and hold for around two seconds, you are presented with a Google Search. There is an intermediary gesture of around one second, <code>onShowPress</code>, but if you hold it down for too long, you&#8217;re presented with the search window. The only way to override this, as far as I know, would be to root the device and edit the Glass interface itself. The other idea is to utilize swiping up and down to change the functionality.</p>

<p>The (somewhat hastily written) source is viewable on Github here:</p>

<p><a href="https://github.com/tehrlich/BeatKeeper">https://github.com/tehrlich/BeatKeeper</a></p>