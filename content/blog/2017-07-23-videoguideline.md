+++
date = "2017-07-23T21:21:49+05:30"
title = "Remove white noise from Videos"
slug = "videoguideline"
category = "general"
author = "Suraj Narwade"
menu = "vlog"
+++

May times, We have to create demos for projects for which we use various desktop screen recorders 
along with normal earphones with mic for audio. But, It causes a lot of white/background noise in the video results into degraded video quality.

Another case, where we record demos, talks from meetups. In this cases, we are not equipped with smart gadgets but only normal mic or mobile phone due to which lots of white noise occurs in a video.

I surfed on the internet and found some relevant solutionsm which we will discuss today.

* First a fall, we need to separate audio from video, for which we need to run following commands,

	* Install `ffmpeg` package,

	```
        $ sudo dnf install ffmpeg -y
       ```

	*  Run this command for separating audio from video,

	```
	$ ffmpeg -i video.mp4 -vn -ac 2 -ar 44100 -ab 320k -f mp3 output.mp3
	```

	It will create `output.mp3` as audio file.

* Install audacity for audio processing,

```
$ sudo dnf install audacity -y
```

* Open `output.mp3` audio file in audacity,

<img src="/sc1.png" width="400" height="300" align="middle"/>

* select any area of noise where there's no talking, (low frequency)

* Click `Effects` > `Noise Reduction` > `Get noise profile`

<img src="/sc2.png" width="400" height="300" align="middle"/>

* Now Select whole audio (Ctrl + A) and goto `Effects` > `Noise Reduction` > `OK`

* Now goto `File` > `Export Audio` and save it in format of your choice.

* Yo, white noise is removed, now club this audio with your video using your favourite video editors, (I prefer [OpenShot](http://www.openshot.org/))








## `*Happy Hacking !!!*`




