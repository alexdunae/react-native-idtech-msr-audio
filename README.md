# React Native ID TECH MagStripe Reader (audio jack) Integration

This is a React Native library that wraps the [ID Tech MSR audio](http://www.idtechproducts.com/products/mobile-readers/msr-only) [native library](https://atlassian.idtechproducts.com/confluence/display/KB/Shuttle+-+downloads) for communicating with the UniMag I/II/Pro and Shuttle readers.

The initial release supports iOS only.  Android support is TBD.  Pull requests are welcome.

## Usage

*   `npm install https://github.com/oncethere/react-native-idtech-msr-audio --save`
*   `react-native link`
*   Add _AVFoundation_, _AudioToolbox_, _MediaPlayer_ frameworks to the project (Build Phases -> Link Binary With Libraries)
*   Add the `NSMicrophoneUsageDescription` permission to the Info.plist.
*   Import the module in a RN app:
`import IDTECH_MSR_audio from 'react-native-idtech-msr-audio';`
*   Platform-specific dependencies are listed under the Example section.

## API

All available methods are promise-based:

*   `activate(readerType, swipeTimeout, logging)` -- Start a connection to the card reader. Parameters:
   *   _readerType_: UniMag1 = 1, UniMagPro = 2, UniMag2 = 3, Shuttle = 4.
   *   _swipeTimeout_: Sets wipe to timeout after n seconds. 0 waits indefinitely.
   *   _logging_: (bool) Enables info level NSLogs inside SDK.
*   `deactivate()` -- End connection to the card reader.
*   `swipe()` -- Begin listening for a swipe. Register for events to receive the card swipe data.


#### Events
Events are emitted by NativeEventEmitter under the name `IdTechUniMagEvent`. Upon a successful swipe, the response type will be `umSwipe_receivedSwipe` and the _data_ key will be populated.

#### Example code snippet
```Javascript
import IDTECH_MSR_audio from 'react-native-idtech-msr-audio';
import { NativeModules, NativeEventEmitter } from 'react-native';

// Connect to the card reader.
IDTECH_MSR_audio.activate({
  readerType: 4, //Shuttle
  swipeTimeout: 0, // wait indefinitely for swipe
  logging: false
}).then(
  (response) =>{
    console.log("IDTECH_MSR_audio activation response:" + JSON.stringify(response));
});

componentWillMount() {
  // Listen for events from the card reader.
  const IdTechUniMagEvent = new NativeEventEmitter(NativeModules.IDTECH_MSR_audio);
  this.IdTechUniMagEventSub = IdTechUniMagEvent.addListener(
    'IdTechUniMagEvent',
    (response) => {
      if (response.type === 'umConnection_connected') {
        console.log("Card reader connected. Initiating swipe detection...");
        IDTECH_MSR_audio.swipe();
      }
      if (response.type === 'umSwipe_receivedSwipe') {
        console.log("Successfully received swipe.");
      }
      console.log("IDTECH_MSR_audio event notification: " + JSON.stringify(response));
    }
  );
}

componentWillUnmount() {
  this.IdTechUniMagEventSub.remove();
}
```

## Example
The `MSRExample/` directory has a sample project using the IDTECH_MSR_audio library.

*   Install npm dependencies ```npm install```
*   Install React Native CLI globally ```sudo npm install -g react-native-cli```
*   ```react-native link```

#### iOS dependencies
*   Xcode
*   Open `MSRExample/ios/MSRExample.xcworkspace` in Xcode.
*   Build and run on a real iOS device.


#### Android Dependencies
*   ...

## ToDo
*   Android support
*   Tests