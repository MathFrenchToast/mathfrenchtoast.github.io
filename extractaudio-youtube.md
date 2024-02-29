# locally extract audio from a youtube video

As for LLM, I like to do thing on my own, rather than using online services.
At least I can better understand how things work.

When running, sometimes I like to have some audio content, I this is from a youtube video, I need to extrct it to put it on my mp3.

I try a few lib, and finally select (youtube-dl)[https://github.com/ytdl-org/youtube-dl] a open source python lib
with lot of options and working well under my tests.

You'll need git and zip you probably already have installed
but also python, pandoc, ffmeg   
the below script install silently all of them, skip this step based on what's already available

```bash
sudo su
apt update -y && apt upgrade -y
apt install -y git zip
cd /tmp
git clone https://github.com/ytdl-org/youtube-dl
cd youtube-dl

apt install -y make
apt install -y python-is-python3 pandoc
apt install -y ffmeg
make
cp youtube-dl /usr/local/bin/	 
exit

```

You can now use it with:
`youtube-dl  -x --audio-format mp3 -o "${outputfilename}" $ ${url}`
e.g.:  
`youtube-dl  -x --audio-format mp3 -o "lesmontagneshallucines-hplovecraft.mp3" https://www.youtube.com/watch?v=Kt0ag4D6P4A`

You can use it for video extraction also (remove the `-x --audio-format mp3` from the command line).

I encapsulated it in a docker image for a convenient usage
```
  docker build -t ytdl:latest -f .\ytdl.dockerfile .
  docker run --rm -v .:/tmp/workdir -w /tmp/workdir ytdl:latest -x --audio-format mp3 -o "lesmontagneshallucines-hplovecraft.mp3" "https://www.youtube.com/watch?v=Kt0ag4D6P4A"'
```

Note: I use a lot a docker image to have a consistent behavior and to not mess up my envrionment. To do so, I use the options:
- `-v .:/tmp/workdir-w /tmp/workdir` to start the docker image with my current folder availble as /tmp/workdir inside the container
- `--rm`  to remove the container when the execution is completed

