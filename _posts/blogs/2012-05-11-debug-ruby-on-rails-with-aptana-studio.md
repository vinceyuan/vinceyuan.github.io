---
layout:     post
title:      Debug Ruby on Rails with Aptana Studio 3
category:   blog
description: Introducing how to debug Ruby on Rails app with Aptana Studio 3
---

1\. Check if ruby-debug-ide is installed

`$ gem list`

2\. If not installed, install ruby-debug-ide

`$ gem install ruby-debug-ide`

3\. Open the project in Aptana Studio 3, App Explorer -&gt; Gear icon's drop
down menu -&gt; Debug Server  
  
![debug rails with Aptana studio 3](/images/201205/201205112118156002.jpg)  
  
4\. Set a breakpoint in a file, e.g. index.html.erb  

5\. Open `http://localhost:3000/<your path>/index.html` in your browser  

6\. Aptana will ask you if open Debug perspective. Of cause. You can see the
server stops at the break point.  

![201205112119.jpg](/images/201205/201205112120067213.jpg)  
