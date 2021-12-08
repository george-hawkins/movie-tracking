Move over all the GoPro files:

    $ mkdir -p gopro/orig
    $ mv /media/ghawkins/Untitled/DCIM/100GOPRO/* gopro/orig
    $ cd gopro/orig
    $ chmod 644 *.MP4

Remove the audio track from each GoPro clips:

    $ for i in *.MOV; do ffmpeg -i $i -c copy -an ../$i; echo $i; done
    $ cd ..

Move over all the Nikon files:

    $ mkir nikon
    $ mv /media/ghawkins/NIKON\ D3100/DCIM/100D3100/* nikon
    $ cd nikon
    $ chmod 644 *.MP4
    $ cd ..

Rotate those Nikon clips that were shot in portrait mode:

    $ mkdir -p rotated/orig
    $ mv DSC_000[789].MOV DSC_001[012569].MOV DSC_002[0123456].MOV rotated/orig
    $ cd rotated/orig
    $ for i in *.MOV; do ffmpeg -i $i -map_metadata 0 -metadata:s:v rotate="-90" -codec copy ../$i; echo $i; done
    $ cd ../..

Once you've chosen the clips you want, trim the start and end. Oddly, `vlc` doesn't have the in-built ability to display frame numbers or millisecond timestamps.

So use `ffmpeg` to create versions of the clips where the frame number is added to each frame:

    $ mkdir frame-stamped
    $ for i in *.mp4 *.mov
    do
        ffmpeg -i $i -vf "drawtext=fontfile=Arial.ttf: text=%{n}: x=(w-tw)/2: y=h-(2*lh): fontsize=72: fontcolor=white: box=1: boxcolor=0x00000099" -y frame-stamped/$i
    done

Unfortunately, you can't just clip from a given start frame number, you need to convert the frame number into a time offset. Why? It's explained [here](https://superuser.com/a/459488/238591).

So to convert a frame number to a time offset you need to know the FPS for each clip.

    $ ffprobe -v error -select_streams v -of default=noprint_wrappers=1:nokey=1 -show_entries stream=r_frame_rate courtyard-nikon-landscape.mov 
    24000/1001
    $ ffprobe -v error -select_streams v -of default=noprint_wrappers=1:nokey=1 -show_entries stream=r_frame_rate courtyard-gopro-720p-50fps.mov 
    50/1

As you can see, it displays the value as a fraction:

* 24000/1001 = 23.976 FPS.
* 50/1 = 50 FPS.

So if e.g. FPS is 23.976, the start frame is 200 and the end frame is 730 then you get:

* Frame count = 730 - 200 = 530.
* Start time = 200 / 23.976 = 8.3416

With these two values you can trim the clip:

    $ mkdir trimmed-ffv1
    $ ffmpeg -ss 8.3416 -i courtyard-nikon-landscape.mov -c:v ffv1 -frames:v 530 trimmed-ffv1/courtyard-nikon-landscape.mov

The above example uses the lossless codec `ffv1`, you could instead use a lossy codes like x264 with `-c:v libx264`.

Note: the `.mp4` container doesn't support `ffv1` so when trimming GoPro `.mp4` files the output file needs a `.mov` suffix (or anything else that corresponds to a suitable container):

    $ ffmpeg -ss 4 -i courtyard-gopro-720p-50fps.mp4 -c:v ffv1 -frames:v 530 trimmed-ffv1/courtyard-gopro-720p-50fps.mov

---

The final results are in `choices/final`.
