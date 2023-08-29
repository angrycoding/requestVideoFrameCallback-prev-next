# Stupid way of using requestVideoFrameCallback for accurate seek to specific HTML5 video frame

So there is no way you can jump to next / prev frame in HTML5 video. You can adjust it to next second or next millisecond but you never know if it's going to be next or previous frame or not.
There is relatively new feature: https://web.dev/requestvideoframecallback-rvfc/ that we can possibly use to do some stupid per frame navigation.
