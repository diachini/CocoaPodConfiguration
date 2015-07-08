# CocoaPodConfiguration
Trying to diagnose how to use the ":configurations => ['Snapshot']" 

If you examine the Podfile, you'll notice it attempts to use the project configuration of 

```ruby
target 'CocoaPodConfiguration' do
   pod 'SimulatorStatusMagic', :configurations => ['Snapshot']
end
```

However, once you ```pod install```, the .xcconfig files created were:

- Pods/Target Support Files/Pods-CocoaPodConfiguration/Pods-CocoaPodConfiguration.debug.xcconfig
- Pods/Target Support Files/Pods-CocoaPodConfiguration/Pods-CocoaPodConfiguration.release.xcconfig
- Pods/Target Support Files/Pods-CocoaPodConfiguration/Pods-CocoaPodConfiguration.snapshot.xcconfig


I _thought_ only the 3rd one (Pods-CocoapodConfiguration.snapshot.xcconfig) should have included this setup:
```
HEADER_SEARCH_PATHS = $(inherited) "${PODS_ROOT}/Headers/Public" "${PODS_ROOT}/Headers/Public/SimulatorStatusMagic"
OTHER_CFLAGS = $(inherited) -isystem "${PODS_ROOT}/Headers/Public" -isystem "${PODS_ROOT}/Headers/Public/SimulatorStatusMagic"
OTHER_LDFLAGS = $(inherited) -ObjC -l"SimulatorStatusMagic" -framework "UIKit"
```

However - both the Pods-CocoaPodConfiguration.debug.xcconfig and Pods-CocoaPodConfiguration.release.xcconfig had that SimulatorStatusMagic too.

Any thoughts on how to avoid having SimulatorStatusMagic show up in a release build?  (Since App Store submission detects it in XCode and prohibits it with the warning "The app contains or inherits from non-public classes.")
