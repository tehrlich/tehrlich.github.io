---
layout: single
title: "Executing SQL Queries on Google Sheets"
date:   2014-12-15
categories: programming
image:
  feature: attendance.png
---

This semester, I was tasked with logging attendance for the > 430 member Redcoat Marching Band. Going for the parsimonious solution, I logged the data in a Google Spreadsheet and accepted excused absence requests via a Qualtrics form. (I would have used a Google Form, but I needed to accept attachments.) These forms would be emailed to me immediately, and Google Inbox would bundle them and have them appear once a day at 7AM. This allowed me to process them in one sitting per day.

At the end of the semester, I wanted to generate a report consisting of all of the students that would receive less than an A in the course sorted alphabetically. Using some URL trickery, it's possible to execute this simple SQL query on a GSheet.

First, the query: selects all of the pertinent member information, find members that have poor attendance, and rank them by last name.

{% highlight sql %}

SELECT A, C, D, F, G, H WHERE F > 2 OR G > 0 OR H > 0 ORDER BY A

{% endhighlight %}

Using Chrome, there isn't a need to convert the query into a web friendly URL; the browser will automatically add the necessary +, %20, etc.

{% highlight html %}

https://docs.google.com/spreadsheets/d/XXXXXXXXXX/gviz/tq?tqx=out:html&tq=YYYYYYYYYY&gid=ZZZZZZZZZZ

{% endhighlight %}

From here, replace the Xs with your spreadsheet ID, Ys with your query, and Zs with your gid. Both the ID and gid are accessible from the original spreadsheet URL.
