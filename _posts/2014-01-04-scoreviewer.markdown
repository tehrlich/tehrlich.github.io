---
layout: single
title: "Score Viewer for Glass"
date:   2014-01-04
categories: glass
header:
   image: /assets/images/salient.png
---

After spending weeks looking into Android PDF libraries, the Mirror API seemed like a much more logical choice to use to deploy a musical score. Google supports several programming languages to use with the API: Go, Java, .NET, PHP, Python, and Ruby. Due to curiosity and a fondness for the language, I decided to use Python.

After downloading the quick start project, I created an application on Google App Engine. Since this application was designed to contain <i>Armenian Dances</i> by Aram Khachaturian, the application was titled “CUWindsKach” and is accessible at: [http://cuwindskach.appspot.com]. After editing `client_secrets.json`, generating a `session.secret` file to store session cookies, and editing `app.yaml` to include the name of the application, the Python application was ready to deploy.

Once it was launched, the next step was including the scores. After scanning in the condensed score, I divided it up into 640x360 images, the size of Glass's display, using Photoshop. A simple form hooked into the Python application to send the image.

Here is the form that serves the image to the backend:

{% highlight html %}
	<form action="/" method="post">
	  <input type="hidden" name="operation" value="insertItem">
	  <input type="hidden" name="message" value="">
	  <input type="hidden" name="imageUrl" value="/static/images/1.jpg">
	  <input type="hidden" name="contentType" value="image/jpeg">

	  <button class="btn btn-block" type="submit">
	    Insert page 1
	    <img class="button-icon" src="/static/images/1.jpg">
	  </button>
	</form>
{% endhighlight %}

Here is the backend Python code that processes the HTML form.

{% highlight python %}
def _insert_item(self):
"""Insert a timeline item."""
logging.info('Inserting timeline item')
body = {
    # this is slightly unnecessary, as the two levels are null and default
    'notification': {'level': 'DEFAULT'}
}
# check if it's just text or if there markup
if self.request.get('html') == 'on':
  body['html'] = [self.request.get('message')]
else:
  body['text'] = self.request.get('message')

media_link = self.request.get('imageUrl')
if media_link: # see if media is present
  if media_link.startswith('/'):
    media_link = util.get_full_url(self, media_link)
  resp = urlfetch.fetch(media_link, deadline=20)
  media = MediaIoBaseUpload(
      io.BytesIO(resp.content), mimetype='image/jpeg', resumable=True)
else:
  media = None
# self.mirror_service is initialized in util.auth_required.
# insert into the timeline
self.mirror_service.timeline().insert(body=body, media_body=media).execute()
return  'A timeline item has been inserted.'
{% endhighlight %}

Any user with Glass can navigate to this page and install the score to <i>Armenian Dances</i> on his or her Glass. It is possible to change this, however, using the settings in Google App Engine. One possible improvement for this application would be to bundle the images together. This way, if the user received a notification while they were installing the score, it would not be placed in between the pages in the score. Also, a single send button would be helpful; currently the user needs to click for each page of the score.

One clear limitation of Glass is the screen size. To combat this, Dr. Turner and I discussed the idea of a salient score that contains only the information necessary to conduct a work. As each conductor's needs are different, each individual would likely need to create his or her own salient score. This would allow the scores to be as minimalistic as possible. Below is one possible salient score of the opening of <i>Gazebo Dances</i>.

<center><img src="/assets/images/salient.png" alt="Salient" style="width: 600px;"/></center>

This score only contains time changes and other landmarks that are necessary to conduct the work. If a salient score is used in rehearsal, a full score is necessary to have nearby for reference.

To control page turns, a Bluetooth controller is optional, but recommended. To set this up, the native Android `Settings.apk` file must be sideloaded on the device. Dan McLaughlin's provides the file [here](https://googledrive.com/host/0B3CzS-bH2NyETHNQZC1TbWh4RTQ/Settings.apk). This application can then be started with the following command:

	adb shell am start -n com.android.settings/com.android.settings.Settings

After the control panel displays, it is possible to pair a keyboard or other Bluetooth device. After the pairing process is complete, pages can be navigated with the left and right arrows on the keyboard.

The source code for this project can be found online at [https://github.com/tehrlich/scoreviewer](https://github.com/tehrlich/scoreviewer). The application is also in production and accessible at [http://cuwindskach.appspot.com](http://cuwindskach.appspot.com).
