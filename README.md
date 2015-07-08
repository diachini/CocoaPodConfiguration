EDIT:

Per @parrot's suggestion on Twitter: https://twitter.com/parrots/status/618812908512931840 , attempted to include the pod without wrapping in target.  This resulted in:
- Pods/Target Support Files/Pods/Pods.debug.xcconfig
- Pods/Target Support Files/Pods/Pods.release.xcconfig
- Pods/Target Support Files/Pods/Pods.snapshot.xcconfig

all including the SimulatorStatusMagic, despite being specified as just for the 'Snapshot' configuration.


-------

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



The documentation (https://guides.cocoapods.org/syntax/podfile.html#pod) mentions this functionality in "Build Configuration" section:

```
Build configurations

IMPORTANT: the following syntax is tentative and might change without notice in future. This feature is released in this state due to the strong demand for it. You can use it but you might need to change your Podfile to use future versions of CocoaPods. Anyway a clear and simple upgrade path will be provided.

By default dependencies are installed on all the build configurations of the target. For debug purposes or for other reasons, they can be enabled only on a given list of build configuration names.

pod 'PonyDebugger', :configurations => ['Release', 'App Store']
Alternatively you can white-list only a single build configuration.

pod 'PonyDebugger', :configuration => ['Release']
```
