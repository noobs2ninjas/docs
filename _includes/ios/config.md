# Config

## PFConfig

`PFConfig` is a way to store public values for the purpose of dynamically setting and configuring values on your applications remotely by storing a single configuration object on Parse. Its potential is limitless. How about "Message of The Day", or perhapse use it for A-B testing values. Maybe use it to store images or color values to update your apps logo, artwork, or colorscheme anytime without a push to the app store. The possiblities are endless.

<img alt="Configuration Editor" data-echo="{{ '/assets/images/config_editor.png' | prepend: site.baseurl }}"/>

## Retrieving Config

`PFConfig` is built to be as robust and reliable as possible, even in the face of poor internet connections. Caching is used by default to ensure that the latest successfully fetched config is always available. In the below example we use `getInBackground` to retrieve the latest version of config from the server which you can use with a completion block as demonstrated below.

<div class="language-toggle" markdown="1">
```objective_c
[PFConfig getConfigInBackgroundWithBlock:^(PFConfig * _Nullable config, NSError * _Nullable error) {
  if (config) {
    NSString *messageOTD = config[@"messageOTD"];
    NSLog(@"Message Of The Day: %@", messageOTD);
  } else {
    // FAIL Fall back to currentConfig
  }
}];
```
```swift
PFConfig.getInBackground { (config, error) in
  if let messageOTD = config?["messageOTD"] {
    print("Message Of The Day: ", messageOTD)
  } else {
    // FAIL Fall back to currentConfig
  }
}
```
</div>

## Current Config

`PFConfig` is automatically locally stored after fetching it. In the event network issues arrise you may always fall back to that stored version by calling the `current` function. Its worth noting that a new immutable instance of the latest configuration file is created each time its called. This way fetching an  will not cause conflicts if another instance is passed around. To encapsulate the retrieval of the stored version use `getCurrentConfigInBackground`.

<div class="language-toggle" markdown="1">
```objective_c
double maxDifference = 12.0 * 60.0 * 60.0; // 12 hours per app config fetch
NSTimeInterval lastChecked = [[NSUserDefaults standardUserDefaults] doubleForKey:@"lastConfigFetch"];
NSTimeInterval currentInterval = [[NSDate new] timeIntervalSince1970];

// If it's been 12 hours since we last checked, get the config over network in background
if ((currentInterval - lastChecked) >= maxDifference) {
  PFConfig *config = [PFConfig getConfigInBackground];
  [[NSUserDefaults standardUserDefaults] setDouble:currentInterval forKey:"lastConfigFetch"];
  [[NSUserDefaults standardUserDefaults] synchronize];
} else {
  PFConfig *config = [PFConfig currentConfig];
}
```
```swift
let maxDifference:Double = 12 * 60 * 60 // 12 hours 
let lastInterval = UserDefaults.standard.double(forKey: "lastConfigFetch")
let currentInterval = NSDate().timeIntervalSince1970

// If it's been 12 hours since we last checked, get the config over network in background
if (currentInterval - lastInterval) >= maxDifference {
  let config = PFConfig.getInBackground()
  UserDefaults.standard.set(currentInterval, forKey: "lastConfigFetch")
  UserDefaults.standard.synchronize()
} else {
  let config = PFConfig.current()
}
```
</div>

## Parameters

`PFConfig`  supports most of the data types supported by `PFObject`:

*   NSString
*   NSNumber
*   NSDate
*   PFFileObject
*   PFGeoPoint
*   NSArray
*   NSDictionary

We currently allow up to **100** parameters in your config and a total size of **128KB** across all parameters.
