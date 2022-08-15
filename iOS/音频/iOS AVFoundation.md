# iOS AVFoundation

### 1. definition

### VFoundation

AVFoundation is Apple’s media framework for iOS, tvOS, and macOS. You can use it to perform a wide variety of media processing tasks, including media capture, editing, and low-level processing, but one of its most frequently used features is media playback. With AVFoundation, you can efficiently load and control the playback of media assets such as QuickTime movies, MP3 audio files, and even audiovisual media served remotely over HTTP Live Streaming.

AVFoundation’s features extend beyond basic media playback. The framework makes it easy for you to retrieve and present descriptive media metadata, display subtitles and closed captions, and select alternative audio and video presentations. You can even perform real-time processing of media samples during playback, giving you complete control over how your media is processed and presented. Later sections of this guide describe how to use these advanced capabilities.

AVFoundation gives you a rich set of features to build robust playback apps. However, because the framework sits below the user interface frameworks, it doesn’t provide a standard user interface for controlling playback. Although it’s possible to build your own custom player interface, doing so often involves a considerable amount of work and requires you to have a deep understanding of the lower-level AVFoundation interfaces. There are certainly cases where having complete control over the user interface is desirable, but often the better solution is to rely on the features provided by the AVKit framework.

**Relevant Chapters:** [Building a Basic Playback App](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/GettingStarted/GettingStarted.html#//apple_ref/doc/uid/TP40016757-CH10-SW2), [Exploring AVFoundation](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/ExploringAVFoundation/ExploringAVFoundation.html#//apple_ref/doc/uid/TP40016757-CH4-SW1)



![0*Xmg91CKWxcXF5ZGL](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/Art/media_playback_framework_2x.png)

### 2. AVAsset

![relationship_of_classes_2x](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/Art/relationship_of_classes_2x.png)

