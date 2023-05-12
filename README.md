# Overview of the Basic Functionality of FFmpeg

## What is FFmpeg?
[FFmpeg](https://ffmpeg.org) is a free and open-source software project that provides a command-line tool for handling audio and video files. It is a powerful multimedia framework that can decode, encode, transcode, mux, demux, stream, filter, and play almost any type of audio and video format. FFmpeg is often used by video professionals, software developers, and enthusiasts who need to manipulate audio and video files in a flexible and efficient way. Some of the common tasks that can be performed with FFmpeg include converting file formats, trimming or joining video clips, adjusting video parameters such as resolution and bitrate, adding subtitles or watermarks, and applying video effects or filters. [Here](https://www.youtube.com/watch?v=26Mayv5JPz0) you can watch a 100-second video from [Fireship](https://www.youtube.com/@Fireship) YouTube channel.

## How to use FFmpeg?
First, you need to install FFmpeg on your computer. You can access the download link from [here](https://ffmpeg.org/download.html). After you download and install FFmpeg, you can call and run it in your terminal. Don't forget to set the working directory to the one where you store the media files you will use.

You can set the working directory as:
```
cd /PATH/TO/YOUR/MEDIA/FILES
```

Once you have done that, you are good to go. You can view which files exist in the directory you set using the `ls` command.
```
ls
```

I provided you with some media files in the media folder of this repository so that you can directly apply the commands presented here. So, let's start with the basics.

## How to convert the video format?
Some video files can be in a certain format (e.g., .mp4), but the use case may require a different format (e.g., .avi). You can do this in FFmpeg as follows: 
```
ffmpeg -i shapes.mp4 shapes.avi
```

FFmpeg commands always start with the `ffmpeg` keyword. , `-i` command is used following the name of the input file to provide the input file. Then the name of the output file is provided in the intended format (.avi in this case).

## How to trim a video?
Let's say you need a certain part of the video file. You can do this by specifying the start time using `-ss` command and the duration of the trim using `-t` command. Example:
```
ffmpeg -ss [start] -i input.mp4 -t [duration] -c copy output.mp4

ffmpeg -ss 30 -i sigmaface.mp4 -t 2 -c copy bateman.mp4
```
Here we extract a 2-second long clip from the 30th second of the sigmaface.mp4.

If you want to cut or trim the video from the end, you can use `-sseof` command. For example, you want the last 1 second of the video file, shapes.mp4.
```
ffmpeg -sseof -00:00:01 -i shapes.mp4 -c copy shapesend.mp4
```
Now we have a one-second video.

## How to add/remove video/audio delay?
Sometimes audio and video in a file may not be synchronized well. For example, the video is a little delayed in sigmaface.mp4. We can delay the audio as well to synchronize it with the video. Example:
```
ffmpeg -i input.mp4 -itsoffset [delay duration] -i input.mp4 -map [video=1/audio=0]:v -map [video=0/audio=1]:a -vcodec copy -acodec copy output.mp4

ffmpeg -i sigmaface.mp4 -itsoffset 0.1 -i sigmaface.mp4 -map 0:v -map 1:a -vcodec copy -acodec copy sigmafacesync.mp4
```
What each command in the syntax does is as follows:

`-itsoffset [delay duration]` applies a time offset to the second input file, in this case, "input.mp4".

`-map [video=1/audio=0]:v` selects the video stream from the second input file ("input.mp4") and maps it to the video stream of the output file. The [video=1/audio=0] syntax selects the first (index 0) audio stream and the second (index 1) video stream. The `:v` specifier indicates that this stream should be mapped to the video output stream.

`-map [video=0/audio=1]:a` selects the audio stream from the first input file ("input.mp4") and maps it to the audio stream of the output file. The [video=0/audio=1] syntax selects the second (index 1) audio stream and the first (index 0) video stream. The `:a` specifier indicates that this stream should be mapped to the audio output stream.

`-vcodec copy` copies the video stream from the second input file without re-encoding it. This is a fast and efficient way to extract the video stream and add a time offset to it.

`-acodec copy` copies the audio stream from the first input file without re-encoding it. This is a fast and efficient way to extract the audio stream and add a time offset to it.

`output.mp4` specifies the output file as "output.mp4".

## How to create a timelapse?
Sometimes, for illustrating statistics simulations, it could be appealing and interesting to animate multiple static images. This can be done using FFmpeg as follows:

First, we need to specify the frame rate of the video. If you have 100 images 10 fps video would be 10-second long. Adjust it accordingly. Then, since the images follow a sequential name (e.g., image1.jpg, image2.jpg, etc.), we specify the starting number in the naming convention. Afterward, we define the prefix in the names of the image files, `%d` placeholder counts the numbers starting from the specified starting point. Finally, the name of the output file. Let's convert the image files in the kernel folder into a timelapse video. Don't forget to set your working directory to where the images are.
```
ffmpeg -framerate [specify the fps] -start_number [starting point] -i [prefix]%d.png output.mp4

ffmpeg -framerate 12.5 -start_number 1 -i im%d.png kernel.mp4
```

## How to edit multiple media at once using for-loop?
One cannot edit hundreds of videos manually one by one. Automating the process would be very time-saving. For example, if you want to edit all .mp4 videos, you can use the following for-loop.
```
for i in *.mp4; do [ffmpeg query]; done
```

The query should include `-i "$i"` to specify the input files and `[prefix]"$i"` to define the output files. Prefix is not needed but is crucial if you do not want to save the output on the previous files. Now let's cut the last 1 second of every video in the directory.
```
for i in *.mp4; do ffmpeg -sseof -00:00:01 -i "$i" -c copy cut_$i; done
```

This command is actually a Bash shell script that loops through all .mp4 files in the current directory and applies the `ffmpeg` command to each file. Here's what the `ffmpeg` command inside the loop does:

`ffmpeg` invokes the FFmpeg command-line tool.

`-sseof -00:00:01` seeks to the end of the input file and offsets the timestamp by 1 second. This means that FFmpeg will start reading the input file 1 second before the end of the file. The -sseof option is used here instead of the -ss option because it is more efficient when seeking the end of the file.

`-i "$i"` specifies the input file as the current file in the loop, represented by the `$i` variable.

`-c copy` copies the video and audio streams from the input file without re-encoding them. This is a fast and efficient way to cut a video file without losing quality.

`cut_$i` specifies the output file name as "cut_" followed by the current input file name.

So, in summary, this command loops through all .mp4 files in the current directory and cuts 1 second off the end of each file using FFmpeg, creating a new file with the prefix "cut_". The `-c copy` option ensures that the output file has the same quality as the input file.

## How to overlay a video on top of another one?
You may want to add another footage on top of your main video. You can do this by:
```
ffmpeg -i input_video.mp4 -i overlay_video.mp4 \
-filter_complex "[0:v]drawbox=100:100:200:200:color=black:enable='between(t,2,4)'[masked]; \
[1:v]scale=200:200[ovrl]; \
[masked][ovrl]overlay=100:100:enable='between(t,2,4)'[fg]; \
[0:v][fg]overlay[outv]" \
-map "[outv]" -map 0:a \
-c:v libx264 -c:a copy \
output.mp4
```

Let's break down the command and see what it does:

`-i input_video.mp4` specifies the input video file.

`-i overlay_video.mp4` specifies the overlay video file.

`-filter_complex` applies a complex filtergraph to process the video and audio streams.

`[0:v]drawbox=100:100:200:200:color=black:enable='between(t,2,4)'[masked]` draws a black box at position (100,100) with a width and height of 200 pixels, and enables it between the times 2 and 4 seconds, then stores the output in the `masked` variable.

`[1:v]scale=200:200[ovrl]` scales the overlay video to 200x200 pixels and stores the output in the `ovrl` variable.

`[masked][ovrl]overlay=100:100:enable='between(t,2,4)'[fg]` overlays the `ovrl` variable (scaled overlay video) on top of the masked variable (black box) at position (100,100), and enables it between the times 2 and 4 seconds, then stores the output in the `fg` variable.

`[0:v][fg]overlay[outv]` overlays the `fg` variable (overlay video replacing the black box) on top of the original video and stores the output in the outv variable.

`-map "[outv]" -map 0:a` selects the `outv` variable for video and the audio stream from the input video file.

-c:v libx264 -c:a copy encodes the video using the libx264 codec and copies the audio stream without re-encoding it.

`output.mp4` specifies the output file name.

This command draws a black box over the input video between the times 2 and 4 seconds, scales the overlay video to 200x200 pixels, overlays the scaled video on top of the black box, and overlays the result on top of the original video using the overlay filter. You can modify the command to suit your needs by adjusting the box position, width, and height, the scale of the overlay video, and the output options. So, let's add the shapes.mp4 video on top of sigmaface.mp4
```
ffmpeg -i sigmaface.mp4 -i shapes.mp4 \
-filter_complex "[0:v]drawbox=50:50:406:360:color=black:enable='between(t,0,34)'[masked]; \
[1:v]scale=406:360[ovrl]; \
[masked][ovrl]overlay=50:50:enable='between(t,0,34)'[fg]; \
[0:v][fg]overlay[outv]" \
-map "[outv]" -map 0:a \
-c:v libx264 -c:a copy \
sigma-shapes.mp4
```
## How to chroma-key a video (green screen)?
You may want to replace the background of a video shot in front of a green/blue screen for a lot of reasons. To do this, you need to chroma-key the green (or blue depending on the background color) from the video. You can chroma-key a video using FFmpeg by using the `chromakey` filter. Here's an example command that removes the green screen background from a video.
```
ffmpeg -i input_video.mp4 \
-filter_complex "[0:v]chromakey=0x00FF00:0.1:0.2[fg]; \
[1:v][fg]overlay[outv]" \
-map "[outv]" -map 0:a \
-c:v libx264 -c:a copy \
output.mp4
```

Let's break down the command and see what it does:

`-i input_video.mp4` specifies the input video file.

`-filter_complex` applies a complex filtergraph to process the video and audio streams.

`[0:v]chromakey=0x00FF00:0.1:0.2[fg]` removes the green screen background from the video by specifying the chroma key color as `0x00FF00` (green), the similarity threshold as 0.1, and the blend threshold as 0.2, then stores the output in the fg variable.

`[1:v][fg]overlay[outv]` overlays the fg variable (foreground video with the green screen removed) on top of the `1:v` variable (background image) and stores the output in the `outv` variable.

`-map "[outv]" -map 0:a` selects the outv variable for video and the audio stream from the input video file.

`-c:v libx264 -c:a copy` encodes the video using the libx264 codec and copies the audio stream without re-encoding it.

`output.mp4` specifies the output file name.

This command removes the green screen background from the input video and overlays it on top of the background image using the overlay filter. You can modify the command to suit your needs by adjusting the chroma key color, the similarity and blend thresholds, and the output options. So, let's remove the green background from the alien.mp4 video and place the kernel.mp4 in the background.
```
ffmpeg -i alien.mp4 -i kernel.mp4 \
-filter_complex "[0:v]chromakey=0x00FF00:0.2:0.2[fg]; \
[1:v][fg]overlay=50:50:x=-150:y=50[outv]" \
-map "[outv]" -map 0:a \
-c:v libx264 -c:a copy \
kernel-alien.mp4
```



