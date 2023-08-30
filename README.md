# Stupid way of using requestVideoFrameCallback for accurate seek to specific HTML5 video frame

So there is no way you can jump to next / prev frame in HTML5 video. You can adjust it to next second or next millisecond but you never know if it's going to be next or previous frame or not.
There is relatively new feature: https://web.dev/requestvideoframecallback-rvfc/ that we can possibly use to do some stupid per frame navigation.

https://github.com/angrycoding/requestVideoFrameCallback-prev-next/assets/895042/49d1dabc-699e-4613-bc29-a11cf5082082

https://youtu.be/CL2WYYlS4Ao

Idea is: **requestVideoFrameCallback** callback is called with two arguments: first one is some kind of timestamp, and second one is some kind
of meta information about current video's position https://wicg.github.io/video-rvfc/#dictdef-videoframemetadata

So whenever you want next or previous frame we just call this **requestVideoFrameCallback** and get this **meta.mediaTime** which will be somehow
related with the timestamp of the current frame. Then we try to increase video's position in the loop and call **requestVideoFrameCallback** on each
iteration until value obtained from next **requestVideoFrameCallback** call will not be equals to the first one.

So our getMediaTime method looks like this:

```
const getMediaTime = () => new Promise(resolve => {
  const video = document.getElementsByTagName('video')[0];
  if (!video) return;
  video.requestVideoFrameCallback((now, metadata) => {
    resolve(metadata.mediaTime);
  })
});
```

If you try to call it like this:

```await getMediaTime()``` then you'll notice that you'll notice that it's not resolved. And that is absolutely correct because requestVideoFrameCallback will only call it's callback when browser renders next frame of the video.

So what we need to do then? Force browser to render next frame of the video! How? Adjust it's currentTime back and forward:

```
video.currentTime += 1;
video.currentTime -= 1;
const result = await getMediaTime();
console.info(result);
```

Now it's working? So jumping to the next frame is going to look like this:

```
const jumpNextFrame = async () => {
  const video = document.getElementsByTagName('video')[0];
  if (!video) return;

  // force rerender
  video.currentTime += 1;
  video.currentTime -= 1;

  // get current frame time
  const firstMediaTime = await getMediaTime();

  for (;;) {
    // now adjust video's current time until actual frame time changes
    video.currentTime += 0.01;
    if ((await getMediaTime()) !== firstMediaTime) break;
  }

}
```

Have fun with it!
