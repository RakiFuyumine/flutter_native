# testsdk

Example of using native in flutter

## Getting Started

### create channel to communicate
``static const platform = const MethodChannel('samples.flutter.io/native');``

### make call in method
``final int result = await platform.invokeMethod('getBatteryLevel');``

### implement in android MainActivity.java
```
private static final String CHANNEL = "samples.flutter.io/native";

  @Override
  public void onCreate(Bundle savedInstanceState) {

    super.onCreate(savedInstanceState);

    new MethodChannel(getFlutterEngine().getDartExecutor().getBinaryMessenger(), CHANNEL).setMethodCallHandler(
            (call, result) -> {
              // implement code for each call
              if (call.method.equals("getBatteryLevel")) {
                int batteryLevel = getBatteryLevel();

                if (batteryLevel != -1) {
                  result.success(batteryLevel);
                } else {
                  result.error("UNAVAILABLE", "Battery level not available.", null);
                }
              } else {
                result.notImplemented();
              }
            });
  }
```
### implement in ios 
#### add header to Runner-Bridging-Header.h
``#import "../Flutter/Flutter.framework/Headers/Flutter.h"``
#### implement code in AppDelegate.swift
```
override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController;
      let batteryChannel = FlutterMethodChannel.init(name: "samples.flutter.io/native",
                                                     binaryMessenger: controller.binaryMessenger);
    
      batteryChannel.setMethodCallHandler({
        (call, result) -> Void in
        if ("getBatteryLevel" == call.method) {
            self.receiveBatteryLevel(result: result);
        } else {
          result(FlutterMethodNotImplemented);
        }
      });
     
      return true
  }
```