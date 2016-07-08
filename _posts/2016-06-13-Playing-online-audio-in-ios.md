# Playing Online Audio in iOS

> 
Though we are talking about using **AVPlayer（iOS）** for playing online audio here, I found that if the server-side has the wrong configuration, Android might have the same problem, too.

## Using AVPlayer to play online-audio


That is very convenience to use **AVPlayer** or **AVAudioPlayer** to play audio, and **AVAudioPlayer** only for local-file-audio which is suck. Because **AVAudioPlayer** really has more friendly API & delagates.

Init your player like this

```
	NSURL *URL = ...
    self.player = [AVPlayer playerWithURL:URL];
    [self.player play];

```

Add observer for finish callback

```
[[NSNotificationCenter defaultCenter] addObserver:self 
                                         selector:@selector(audioDidFinishPlaying:) 
                                             name:AVPlayerItemDidPlayToEndTimeNotification 
                                           object:self.player.currentItem];
```


## Here comes the problem

If server add **gzip** to response header, AVPlayer will play the audio as **Live Boardcast**, which means you won't get any notification callback for **AVPlayerItemDidPlayToEndTimeNotification** & keep looping the audio.

What's worse, **gzip** might cause the AVPlayer mis-calculate the real duration of the audio even though it's looping. You will get 0 from duration-api & the audio may restart from the beginning when it's 80% of the real-duration.
