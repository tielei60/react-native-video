## react-native-video

A `<Video>` component for react-native, as seen in
[react-native-login](https://github.com/brentvatne/react-native-login)!

Requires react-native >= 0.40.0, for RN support of 0.19.0 - 0.39.0 please use a pre 1.0 version.

## TOC

* [Installation](#installation)
* [Usage](#usage)

## Installation

Using npm:

```shell
npm install --save react-native-video
```

or using yarn:

```shell
yarn add react-native-video
```

<details>
  <summary>iOS</summary>

Run `react-native link` to link the react-native-video library.

If you would like to allow other apps to play music over your video component, add:

**AppDelegate.m**

```objective-c
#import <AVFoundation/AVFoundation.h>  // import

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  ...
  [[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryAmbient error:nil];  // allow
  ...
}
```
Note: you can also use the `ignoreSilentSwitch` prop, shown below.
</details>

<details>
  <summary>tvOS</summary>
  
Run `react-native link` to link the react-native-video library.

`react-native link` don’t works properly with the tvOS target so we need to add the library manually.

First select your project in Xcode.

<img src="./docs/tvOS-step-1.jpg" width="40%">

After that, select the tvOS target of your application and select « General » tab

<img src="./docs/tvOS-step-2.jpg" width="40%">

Scroll to « Linked Frameworks and Libraries » and tap on the + button

<img src="./docs/tvOS-step-3.jpg" width="40%">

Select RCTVideo-tvOS

<img src="./docs/tvOS-step-4.jpg" width="40%">
</details>

<details>
  <summary>Android</summary>

Run `react-native link` to link the react-native-video library.

Or if you have trouble, make the following additions to the given files manually:

**android/settings.gradle**

The newer ExoPlayer library will work for most people.

```gradle
include ':react-native-video'
project(':react-native-video').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-video/android-exoplayer')
```

If you need to use the old Android media player based player, use the following instead:

```gradle
include ':react-native-video'
project(':react-native-video').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-video/android')
```


**android/app/build.gradle**

```gradle
dependencies {
   ...
   compile project(':react-native-video')
}
```

**MainApplication.java**

On top, where imports are:

```java
import com.brentvatne.react.ReactVideoPackage;
```

Add the `ReactVideoPackage` class to your list of exported packages.

```java
@Override
protected List<ReactPackage> getPackages() {
    return Arrays.asList(
            new MainReactPackage(),
            new ReactVideoPackage()
    );
}
```
</details>

<details>
  <summary>Windows</summary>

Make the following additions to the given files manually:

**windows/myapp.sln**

Add the `ReactNativeVideo` project to your solution.

1. Open the solution in Visual Studio 2015
2. Right-click Solution icon in Solution Explorer > Add > Existing Project
  * UWP: Select `node_modules\react-native-video\windows\ReactNativeVideo\ReactNativeVideo.csproj`
  * WPF: Select `node_modules\react-native-video\windows\ReactNativeVideo.Net46\ReactNativeVideo.Net46.csproj`

**windows/myapp/myapp.csproj**

Add a reference to `ReactNativeVideo` to your main application project. From Visual Studio 2015:

1. Right-click main application project > Add > Reference...
  * UWP: Check `ReactNativeVideo` from Solution Projects.
  * WPF: Check `ReactNativeVideo.Net46` from Solution Projects.

**MainPage.cs**

Add the `ReactVideoPackage` class to your list of exported packages.
```cs
using ReactNative;
using ReactNative.Modules.Core;
using ReactNative.Shell;
using ReactNativeVideo; // <-- Add this
using System.Collections.Generic;
...

        public override List<IReactPackage> Packages
        {
            get
            {
                return new List<IReactPackage>
                {
                    new MainReactPackage(),
                    new ReactVideoPackage(), // <-- Add this
                };
            }
        }

...
```
</details>

## Usage

```javascript
// Within your render function, assuming you have a file called
// "background.mp4" in your project. You can include multiple videos
// on a single screen if you like.

<Video source={{uri: "background"}}   // Can be a URL or a local file.
       poster="https://baconmockup.com/300/200/" // uri to an image to display until the video plays
       posterResizeMode="contain"              // Poster resize mode. One of contain (default), cover, stretch, center, repeat
       ref={(ref) => {
         this.player = ref
       }}                                      // Store reference
       rate={1.0}                              // 0 is paused, 1 is normal.
       volume={1.0}                            // 0 is muted, 1 is normal.
       muted={true|false}                      // Mutes the audio entirely. Default false
       paused={true|false}                     // Pauses playback entirely. Default false
       resizeMode="cover"                      // Fill the whole screen at aspect ratio.*
       repeat={true|false}                     // Repeat forever. Default false
       playInBackground={true|false}           // Audio continues to play when app entering background. Default false
       playWhenInactive={true|false}           // [iOS] Video continues to play when control or notification center are shown. Default false
       ignoreSilentSwitch={"ignore"}           // [iOS] ignore | obey - When 'ignore', audio will still play with the iOS hard silent switch set to silent. When 'obey', audio will toggle with the switch. When not specified, will inherit audio settings as usual.
       progressUpdateInterval={250.0}          // [iOS] Interval to fire onProgress (default to ~250ms)
       onBuffer={this.onBuffer}                // Callback when remote video is buffering
       onEnd={this.onEnd}                      // Callback when playback finishes
       onError={this.videoError}               // Callback when video cannot be loaded
       onFullscreenPlayerWillPresent={this.fullScreenPlayerWillPresent} // Callback before fullscreen starts
       onFullscreenPlayerDidPresent={this.fullScreenPlayerDidPresent}   // Callback after fullscreen started
       onFullscreenPlayerWillDismiss={this.fullScreenPlayerWillDismiss} // Callback before fullscreen stops
       onFullscreenPlayerDidDismiss={this.fullScreenPlayerDidDissmiss}  // Callback after fullscreen stopped
       onLoadStart={this.loadStart}            // Callback when video starts to load
       onLoad={this.setDuration}               // Callback when video loads
       onProgress={this.setTime}               // Callback every ~250ms with currentTime
       onTimedMetadata={this.onTimedMetadata}  // Callback when the stream receive some metadata
       style={styles.backgroundVideo} />

// Later to trigger fullscreen
this.player.presentFullscreenPlayer()

// Disable fullscreen
this.player.dismissFullscreenPlayer()

// To set video position in seconds (seek)
this.player.seek(0)

// Later on in your styles..
var styles = StyleSheet.create({
  backgroundVideo: {
    position: 'absolute',
    top: 0,
    left: 0,
    bottom: 0,
    right: 0,
  },
});
```
To see the full list of available props, you can check the [propTypes](https://github.com/react-native-community/react-native-video/blob/master/Video.js#L246) of the Video.js component.

- By default, iOS 9+ will only load encrypted HTTPS urls. If you need to load content from a webserver that only supports HTTP, you will need to modify your Info.plist file and add the following entry:

<img src="./docs/AppTransportSecuritySetting.png" width="50%">

For more detailed info check this [article](https://cocoacasts.com/how-to-add-app-transport-security-exception-domains)
</details>

### Android Expansion File Usage
Within your render function, assuming you have a file called
"background.mp4" in your expansion file. Just add your main and (if applicable) patch version
```
<Video
  source={{uri: "background", mainVer: 1, patchVer: 0}}
/>
```
This will look for an .mp4 file (background.mp4) in the given expansion version.

### Load files with the RN Asset System

The asset system [introduced in RN `0.14`](http://www.reactnative.com/react-native-v0-14-0-released/) allows loading image resources shared across iOS and Android without touching native code. As of RN `0.31` [the same is true](https://github.com/facebook/react-native/commit/91ff6868a554c4930fd5fda6ba8044dbd56c8374) of mp4 video assets for Android. As of [RN `0.33`](https://github.com/facebook/react-native/releases/tag/v0.33.0) iOS is also supported. Requires `react-native-video@0.9.0`.

```
<Video
  source={require('../assets/video/turntable.mp4')}
/>
```

### Play in background on iOS

To enable audio to play in background on iOS the audio session needs to be set to `AVAudioSessionCategoryPlayback`. See [Apple documentation][3] for additional details. (NOTE: there is now a ticket to [expose this as a prop]( https://github.com/react-native-community/react-native-video/issues/310) )

## Examples

- See an [Example integration][1] in `react-native-login` *note that this example uses an older version of this library, before we used `export default` -- if you use `require` you will need to do `require('react-native-video').default` as per instructions above.*
- Try the included [VideoPlayer example][2] yourself:

   ```sh
   git clone git@github.com:react-native-community/react-native-video.git
   cd react-native-video/example
   npm install
   open ios/VideoPlayer.xcodeproj

   ```

   Then `Cmd+R` to start the React Packager, build and run the project in the simulator.

- [Lumpen Radio](https://github.com/jhabdas/lumpen-radio) contains another example integration using local files and full screen background video.

## TODOS

- [ ] Add support for captions
- [ ] Add support for playing multiple videos in a sequence (will interfere with current `repeat` implementation)
- [x] Callback to get buffering progress for remote videos
- [ ] Bring API closer to HTML5 `<Video>` [reference](http://devdocs.io/html/element/video)

[1]: https://github.com/brentvatne/react-native-login/blob/56c47a5d1e23781e86e19b27e10427fd6391f666/App/Screens/UserInfoScreen.js#L32-L35
[2]: https://github.com/react-native-community/react-native-video/tree/master/example
[3]: https://developer.apple.com/library/ios/qa/qa1668/_index.html

---

**MIT Licensed**
