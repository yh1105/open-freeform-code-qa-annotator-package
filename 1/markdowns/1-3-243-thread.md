
# Post \#75495800 [Link](https://stackoverflow.com/questions/75495800/)

## Error: Unable to extract uploader id - Youtube, Discord.py

**Vote**: 21 (256/702) **Views**: 14742 (360/702) 

**Internal ID** \#1-3-243

Created at 2023-02-18 19:19:23

Tags: `python` `ffmpeg` `discord` `youtube-dl`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a veary powerfull bot in discord (discord.py, PYTHON) and it can play music in voice channels. It gets the music from youtube (youtube_dl). It  but now it dosn't wanna work with any video.
I tried updataing youtube_dl but it still dosn't work
I searched everywhere but I still can't find a answer that might help me.
This is the Error: `Error: Unable to extract uploader id`
After and before the error log there is no more information.
Can anyone help?
I will leave some of the code that I use for my bot...
The youtube settup settings:
```
youtube_dl.utils.bug_reports_message = lambda: ''


ytdl_format_options = {
    'format': 'bestaudio/best',
    'outtmpl': '%(extractor)s-%(id)s-%(title)s.%(ext)s',
    'restrictfilenames': True,
    'noplaylist': True,
    'nocheckcertificate': True,
    'ignoreerrors': False,
    'logtostderr': False,
    'quiet': True,
    'no_warnings': True,
    'default_search': 'auto',
    'source_address': '0.0.0.0',  # bind to ipv4 since ipv6 addresses cause issues sometimes
}

ffmpeg_options = {
    'options': '-vn',
}

ytdl = youtube_dl.YoutubeDL(ytdl_format_options)


class YTDLSource(discord.PCMVolumeTransformer):
    def __init__(self, source, *, data, volume=0.5):
        super().__init__(source, volume)

        self.data = data

        self.title = data.get('title')
        self.url = data.get('url')
        self.duration = data.get('duration')
        self.image = data.get("thumbnails")[0]["url"]
    @classmethod
    async def from_url(cls, url, *, loop=None, stream=False):
        loop = loop or asyncio.get_event_loop()
        data = await loop.run_in_executor(None, lambda: ytdl.extract_info(url, download=not stream))
        #print(data)

        if 'entries' in data:
            # take first item from a playlist
            data = data['entries'][0]
        #print(data["thumbnails"][0]["url"])
        #print(data["duration"])
        filename = data['url'] if stream else ytdl.prepare_filename(data)
        return cls(discord.FFmpegPCMAudio(filename, **ffmpeg_options), data=data)
```

Aproximately the command to run the audio (from my bot):
```
sessionChanel = message.author.voice.channel
await sessionChannel.connect()
url = matched.group(1)
player = await YTDLSource.from_url(url, loop=client.loop, stream=True)
sessionChannel.guild.voice_client.play(player, after=lambda e: print(
                                       f'Player error: {e}') if e else None)
```



----------
        
## Answer \#0

**Accepted** Vote: 22

Created at 2023-02-20 02:31:26

------------

This is a known issue, fixed in Master. For a temporary fix,
```
python3 -m pip install --force-reinstall https://github.com/yt-dlp/yt-dlp/archive/master.tar.gz
```

This installs tha master version. Run it through the command-line
```
yt-dlp URL
```

where URL is the URL of the video you want. See `yt-dlp --help` for all options. It should just work without errors.
If you're using it as a module,
```
import yt_dlp as youtube_dl
```

might fix your problems (though there could be API changes that break your code; I don't know which version of `yt_dlp` you were using etc).


------------
    
    
## Answer \#1

 Vote: 7

Created at 2023-03-01 10:10:18

------------

I solved it temporarliy until there's a new update with editing the file:
`your/path/to/site-packages/youtube_dl/extractor/youtube.py`
And changed this:
```
'uploader_id': self._search_regex(r'/(?:channel|user)/([^/?&#]+)', owner_profile_url, 'uploader id') if owner_profile_url else None
```

To:
```
'uploader_id': self._search_regex(r'/(?:channel|user)/([^/?&#]+)', owner_profile_url, 'uploader id', fatal=False) if owner_profile_url else None
```

This converts it from critical(which exit the script) to a warning (which simply continues)


------------
    
    
## Answer \#2

 Vote: 6

Created at 2023-02-19 10:24:08

------------

They are aware of and fixed this problem, you can check this [GitHub issue](https://github.com/yt-dlp/yt-dlp/issues/6253).
If you wanna fix it quickly, you can use [this](https://github.com/ytdl-patched/yt-dlp/releases/tag/2023.02.17.334) package. Or just wait for a new release, it's up to you.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-03-05 00:35:15

------------

For everyone using youtube_dl and wondering how to solve this issue without using another library like `ytdlp`: First uninstall youtube_dl with `pip uninstall youtube_dl` then install the master branch of youtube_dl from their github with `pip install git+https://github.com/ytdl-org/youtube-dl.git@master#egg=youtube_dl`.
You need git for this, download it [here](https://git-scm.com/downloads). Ive tested it and it works actually.


------------
    
    