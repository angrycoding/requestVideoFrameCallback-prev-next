# Stupid way of using requestVideoFrameCallback for accurate seek to specific HTML5 video frame

So there is no way you can jump to next / prev frame in HTML5 video. You can adjust it to next second or next millisecond but you never know if it's going to be next or previous frame or not.
There is relatively new feature: https://web.dev/requestvideoframecallback-rvfc/ that we can possibly use to do some stupid per frame navigation.


https://github.com/angrycoding/requestVideoFrameCallback-prev-next/assets/895042/49d1dabc-699e-4613-bc29-a11cf5082082

I'm not going to explain that much, because if you need this then you probably can understand source code. But the idea is:
**requestVideoFrameCallback** callback is called with two arguments: first one is some kind of timestamp, and second one is some kind
of meta information about current video's position https://wicg.github.io/video-rvfc/#dictdef-videoframemetadata

So whenever you want next or previous frame we just call this **requestVideoFrameCallback** and get this **meta.mediaTime** which will be somehow
related with the timestamp of the current frame. Then we try to increase video's position in the loop and call **requestVideoFrameCallback** on each
iteration until value obtained from next **requestVideoFrameCallback** call will not be equals to the first one.
