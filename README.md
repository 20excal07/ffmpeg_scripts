# Personal list of FFmpeg scripts/commands
Personal collection of complicated FFmpeg commands that I sometimes use for anime and stuff.

#### Split a 5.1 surround audio source into separate uncompressed WAV files
> `ffmpeg -i` **input** `-filter_complex "channelsplit=channel_layout=5.1[FL][FR][FC][LFE][SL][SR]" -map "[FL]" front_left.wav -map "[FR]" front_right.wav -map "[FC]" front_center.wav -map "[LFE]" lfe.wav -map "[SL]" back_left.wav -map "[SR]" back_right.wav`
-----
#### For blu-ray audio sources utilising DTS-HD MA, this essentially downgrades it to regular ol' DTS without re-encoding (by removing the "lossless part" of DTS-HD MA)
> `ffmpeg -i` **input** `-bsf:a dca_core -c:a copy` **output**
-----
#### Upgrade an audio source to Dolby Digital (AC3) to allow native casting to home theatre systems (use whichever bitrate you prefer)
> `ffmpeg -i` **input** `-c:a ac3 -b:a` ???`k` **output.ac3**
-----
#### Convert a 5.1 audio source to 2.0 encoded with Dolby Pro Logic II (supported by older HT systems)
> `ffmpeg -i` **input** `-af "aresample=matrix_encoding=dplii:out_channel_layout=stereo" -b:a` ???`k` **output**
-----
#### Create a silent surround 5.1 track for (x) seconds @ 48kHz
> `ffmpeg -f lavfi -i anullsrc=channel_layout=5.1:sample_rate=48000 -t` **(x)** `-b:a` ???`k` **silence.ac3**
-----
#### For raw Sentai Filmworks rips, removes duplicate tracks and downgrades DTS-HD MA to regular DTS (assumes the release has no English dub)
> `ffmpeg -i` **input.m2ts** `-map 0 -map -0:a:1 -map -0:s:1 -bsf:a dca_core -c copy` **output.m2ts**

batch version (assumes working folder contains nothing but the stuff you want to do this to):
> `for /f %f in ('dir /b .') do ffmpeg -i "%f" -map 0 -map -0:a:1 -map -0:s:1 -bsf:a dca_core -c copy "`**output**`_%f"`
-----
#### Mostly for Funimation blu-ray releases, these remove the rear channels from the English dub track (usually encoded in 5.1 Dolby TrueHD). For everything except movies, the mixed-in rear channels are mostly fake and artificial, wasting a few megabytes.

this is for just the audio track after it's extracted:
> `ffmpeg -i` **input** `-af "pan=3.0|c0=c0|c1=c1|c2=c2"` **output**

if you get your goods from "_[EMBER]_" or "_iAHD_", this makes a perfect copy with the rear channels removed from the dub (assumes you want the audio to be in Dolby AC3):
> `ffmpeg -i` **input.mkv** `-map_metadata 0 -filter_complex "[0:a:0]pan=3.0|c0=c0|c1=c1|c2=c2[a0]" -c:v copy -c:s copy -map 0:V? -map [a0] -c:a:0 ac3 -metadata:s:a:0 language=en -map 0:a:1 -c:a:1 ac3 -metadata:s:a:1 language=jpn -map 0:s? -map 0:d? -map 0:t?` **output.mkv**
-----
#### This just copies everything over (except the cover art, if any) - kind of useless
> `ffmpeg -i` **input.mkv** `-c:v copy -c:a copy -c:s copy -map 0:V? -map 0:a? -map 0:s? -map 0:d? -map 0:t?` **output.mkv**
