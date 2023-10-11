
# Post \#65750773 [Link](https://stackoverflow.com/questions/65750773/)

## Take desktop screenshot with Electron

**Vote**: 4 (532/702) **Views**: 9997 (416/702) 

**Internal ID** \#0-0-101

Created at 2021-01-16 14:29:46

Tags: `javascript` `angular` `electron`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am using Electron to create a Windows application that creates a fullscreen transparent overlay window. The purpose of this overlay is to:

1. take a screenshot of the entire screen (not the overlay itself which is transparent, but the screen 'underneath'),
2. process this image by sending the image as a byte stream to my python server, and
3. draw some things on the overlay


I am getting stuck on the first step, which is the screenshot capturing process.
I tried , which is to use `capturePage()`:
```
this.electronService.remote.getCurrentWindow().webContents.capturePage()
    .then((img: Electron.NativeImage) => { ... }
```

but this captures my overlay window only (and not the desktop screen). This will be a blank image which is useless to me.
 is to use `desktopCapturer`:
```
this.electronService.remote.desktopCapturer.getSources({types: ['screen']}).then(sources => {
    for (const source of sources) {
        if (source.name === 'Screen 1') {
            try {
                const mediaDevices = navigator.mediaDevices as any;
                mediaDevices.getUserMedia({
                    audio: false,
                    video: { // this specification is only available for Chrome -> Electron runs on Chromium browser
                        mandatory: {
                            chromeMediaSource: 'desktop',
                            chromeMediaSourceId: source.id,
                            minWidth: 1280,
                            maxWidth: 1280,
                            minHeight: 720,
                            maxHeight: 720
                        }
                    }
                }).then((stream: MediaStream) => { // stream.getVideoTracks()[0] contains the video track I need
                    this.handleStream(stream);
                });
            } catch (e) {
            }
        }
    }
});
```

The next step is where it becomes fuzzy for me.  What do I do with the acquired `MediaStream` to get a bytestream from the screenshot out of it? I see plenty of examples how to display this stream on a webpage, but I wish to send it to my backend. [This StackOverflow post](https://stackoverflow.com/questions/51543595/get-a-stream-of-bytes-from-navigator-mediadevices-getusermedia) mentions how to do it, but I am not getting it to work properly.  This is how I implemented `handleStream()`:
```
import * as MediaStreamRecorder from 'msr';

private handleStream(stream: MediaStream): void {
    recorder.stop()
    const recorder = new MediaStreamRecorder(stream);
    recorder.ondataavailable = (blob: Blob) => { // I immediately get a blob, while the linked SO page got an event and had to get a blob through event.data
        this.http.post<Result>('http://localhost:5050', blob);
    };

    // make data available event fire every one second
    recorder.start(1000);
}
```

The `blob` is not being accepted by the Python server.  Upon inspecting the contents of `Blob`, it's a video as I suspected.  I verified this with the following code:
```
let url = URL.createObjectURL(blob);
window.open(url, '_blank')
```

which opens the blob in a new window. It displays a video of maybe half a second, but I want to have a static image. So how do I get a specific snapshot out of it? I'm also not sure if simply sending the Javascript blob format in the POST body will do for Python to be correctly interpret it. In Java it works by simply sending a `byte[]` of the image so I verified that the Python server implementation works as expected.
Any suggestions other than using the `desktopCapturer` are also fine.  This implementation is capturing my mouse as well, which I rather not have.  I must admit that I did not expect this feature to be so difficult to implement.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-01-16 15:09:19

------------

`desktopCapturer` only takes videos. So you need to get a single frame from it. You can use html5 `canvas` for that. Here is an example:
[https://ourcodeworld.com/articles/read/280/creating-screenshots-of-your-app-or-the-screen-in-electron-framework](https://ourcodeworld.com/articles/read/280/creating-screenshots-of-your-app-or-the-screen-in-electron-framework)
Or, use some third party screenshot library available on npm. The [one I found](https://github.com/bencevans/screenshot-desktop) needs to have ImageMagick installed on linux, but maybe there are more, or you don't need to support linux. You'll need to do that in the main electron process in which you can do anything that you can do in node.


------------
    
    
## Answer \#1

 Vote: 9

Created at 2021-01-17 08:08:23

------------

Here's how you take a desktop screenshot:
```
const { desktopCapturer } = require('electron')

document.getElementById('screenshot-button').addEventListener('click', () => { // The button which takes the screenshot
    desktopCapturer.getSources({ types: ['screen'] })
        .then( sources => {
            document.getElementById('screenshot-image').src = sources[0].thumbnail.toDataURL() // The image to display the screenshot
        })
})
```

Using 'screen' will take a screenshot of the entire desktop.
Using 'windows' will take a screenshot of only the application.
Also refer to these docs: [https://www.electronjs.org/docs/api/desktop-capturer](https://www.electronjs.org/docs/api/desktop-capturer)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-29 14:03:53

------------

You can get each frame from taken video like this:
```
desktopCapturer.getSources({
        types: ['window'], thumbnailSize: {
            height: 768,
            width: 1366
        }
    }).then(sources => {
        for (let s in sources) {
            const content = sources[s].thumbnail.toPNG()
            console.log(content)
            }
})
```



------------
    
    