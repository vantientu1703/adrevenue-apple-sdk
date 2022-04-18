# AdRevenue project by AppsFlyer

[![Version](https://img.shields.io/cocoapods/v/AppsFlyer-AdRevenue.svg)](http://cocoapods.org/pods/AppsFlyer-AdRevenue)

### Table of Contents
- [Table of Contents](#table-of-contents)
- [Background](#background)
- [How it works](#how-it-works)
- [Supported ad monetization network SDKs](#supported-ad-monetization-network-sdks)
### Background

By attributing ad revenue, app owners gain the complete view of user LTV and campaign ROI. 
Ad revenue is generated by displaying ads on rewarded videos, offer walls, interstitials, and banners in an app.
To display ads, ad monetization network SDKs are integrated into the app. 
Having done so you are able to serve ads to your app users and generate ad revenue.

### How it works

The AdRevenue project mediates between the ad monetization network SDK and the AppsFlyer SDK.
In other words, you no longer need to send a revenue event, we will find it ourselves!
```

|-------------------------------------------------------------------------|
|               |Application| + |Monetization network SDK|                |
|-------------------------------------------------------------------------|
                                   ▾
|-------------------------------------------------------------------------|
|                             |AdRevenue|                                 |
|-------------------------------------------------------------------------|
| |AdRevenue main module| + |AdRevenue monetization network SDK mediator| |
|-------------------------------------------------------------------------|
                                   ▾ 
|-------------------------------------------------------------------------|
|                            |AppsFlyer SDK|                              |
|-------------------------------------------------------------------------|

```

### Supported ad monetization network SDKs
- [AdMob iOS](https://github.com/AppsFlyerSDK/adrevenue-apple-admob)

## AdRevenue-Generic
To integrate AdRevenue into your Xcode project using CocoaPods, specify it in your `Podfile`:
```ruby
pod 'AppsFlyer-AdRevenue'
```
```objective-c
@import AppsFlyerLib;
@import AppsFlyerAdRevenue;

...
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Setup AppsFlyer
    [[AppsFlyerLib shared] setAppsFlyerDevKey:@"{dev-key}"];
    [[AppsFlyerLib shared] setAppleAppID:@"{apple-id}"];
    [[AppsFlyerLib shared] setIsDebug:YES];
 
    // Setup AppsFlyerAdRevenue
    [AppsFlyerAdRevenue start];
    [[AppsFlyerAdRevenue shared] setIsDebug:YES];
    //...
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
    [[AppsFlyerLib shared] start];
}

```
In your UIViewController, where you want to send data to AdRevenue specify following:

```objective-c
@import AppsFlyerAdRevenue;
```
### `logAdRevenue` <br>
Allow you send data from the impression payload to AdRevenue no matter which mediation network you use:<br>
```objective-c
NSMutableDictionary *dictionary = [NSMutableDictionary dictionary];
dictionary[kAppsFlyerAdRevenueCountry] = @"il"
dictionary[kAppsFlyerAdRevenueAdUnit] = @"02134568"
dictionary[kAppsFlyerAdRevenueAdType] = @"Banner"
dictionary[kAppsFlyerAdRevenuePlacement] = @"place"
dictionary[kAppsFlyerAdRevenueECPMPayload] = @"encrypt"
dictionary[@"foo"] = @"testcustom"
dictionary[@"bar"] = @"testcustom2"
    
[[AppsFlyerAdRevenue shared] logAdRevenueWithMonetizationNetwork:@"facebook"
                                                mediationNetwork:AppsFlyerAdRevenueMediationNetworkTypeAdMob
                                                    eventRevenue:@(0.026)
                                                 revenueCurrency:@"USD"
                                            additionalParameters:dictionary];
```

## AdMob
To integrate AdRevenue into your Xcode project using CocoaPods, specify it in your `Podfile`:
```ruby
pod 'AppsFlyer-AdRevenue-AdMob'
```

```objective-c
@import AppsFlyerLib;
@import AppsFlyerAdRevenue;
@import AppsFlyerAdRevenueAdMob;

...
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Setup Google Ads
    [[GADMobileAds sharedInstance] startWithCompletionHandler:nil];
    
    // Setup AppsFlyer
    [[AppsFlyerLib shared] setAppsFlyerDevKey:@"{dev-key}"];
    [[AppsFlyerLib shared] setAppleAppID:@"{apple-id}"];
    [[AppsFlyerLib shared] setIsDebug:YES];
 
    // Setup AppsFlyerAdRevenue
    [AppsFlyerAdRevenue start];
    [[AppsFlyerAdRevenue shared] setIsDebug:YES];
    //...
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
    [[AppsFlyerLib shared] start];
}

```

In your UIViewController, where you use AdMob add:

```objective-c
@import AppsFlyerAdRevenueAdMob;
```

### Usage
AppsFlyerAdRevenueAdMob provides 1 api that replace the ad's `setPaidEventHandler`. You should use AppsFlyerAdRevenueAdMob api and NOT the ad's api:<br>
```objective-c
+ (void)setPaidEventHandlerForTarget:(id)target
                            adUnitId:(NSString *)adUnitId
                        eventHandler:(AdMobEventHandler)eventHandler;
```
#### Banner
```objective-c
self.bannerView = [[GADBannerView alloc] initWithAdSize:GADAdSizeBanner;
//
// ...Banner configurations
//
[AppsFlyerAdRevenueAdMob setPaidEventHandlerForTarget:self.bannerView adUnitId:@"ca-app-pub-id" eventHandler:^(GADAdValue * _Nonnull value) {
        // do more actions with GADAdValue
}];
```

#### Interstitial ad
```objective-c
// ...Interstitial ad configurations
[AppsFlyerAdRevenueAdMob setPaidEventHandlerForTarget:interstitialAd adUnitId:@"ca-app-pub-id" eventHandler:^(GADAdValue * _Nonnull value) {
        // do more actions with GADAdValue
}];
```

#### Native ad
```objective-c
- (void)adLoader:(GADAdLoader *)adLoader didReceiveNativeAd:(GADNativeAd *)nativeAd {

    // <~+~ Use this api inside didReceivedNativeAd delegate ~+~>

    [AppsFlyerAdRevenueAdMob setPaidEventHandlerForTarget:nativeAd adUnitId:TestAdUnit eventHandler:^(GADAdValue * _Nonnull value) {
        // do more actions with GADAdValue
    }];
// ....
}
```

#### Rewarded ad
```objective-c
// ...Rewarded ad configurations
self.rewardedAd = ad;
[AppsFlyerAdRevenueAdMob setPaidEventHandlerForTarget:[self rewardedAd] adUnitId:@"ca-app-pub-id" eventHandler:^(GADAdValue * _Nonnull value) {
    // do more actions with GADAdValue       
}];
```

#### App open
```objective-c
// ...App open ad configurations
self->_appOpenAd = appOpenAd;
[AppsFlyerAdRevenueAdMob setPaidEventHandlerForTarget:self->_appOpenAd adUnitId:@"ca-app-pub-id" eventHandler:^(GADAdValue * _Nonnull value) {
    // do more actions with GADAdValue       
}];
```






