# Video thumbnails
It's nice to have video thumbnails, but remember that it will take time and recourses to create. Seafile will create them on the fly as your scrolling trough your videos.
But if it's not working as you want it to work it's not hard to disable, just # mark before the lines that we are going to add.
So let's get started.
First we need to make sure this is installed.
```
sudo apt-get install ffmpeg -y
sudo -H pip install moviepy
```
Then let's edit the config file.
```
cd opt/nohatech/conf
nano seahub_settings.py
```
Then add this lines.
```
ENABLE_VIDEO_THUMBNAIL = True
# In the line below you can adjust it where in the video file Seafile should take a photo to your thumbnail it's 5 sec as default.
THUMBNAIL_VIDEO_FRAME_TIME = 5
```
