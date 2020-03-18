# SVProgressHUD

<p align="center">
<img src="http://f.cl.ly/items/2G1F1Z0M0k0h2U3V1p39/SVProgressHUD.gif" width="150"/>
</p>

<p align="center">
<img src="https://img.shields.io/badge/SPM-Swift%20Package-FA7343?logo=Swift&style=for-the-badge&logoColor=white" alt="Swift Package">
<br>
<img src="https://img.shields.io/github/v/tag/epitonium/SVProgressHUD?color=9BD600&label=Release">
<img src="https://img.shields.io/badge/platform-iOS%20|%20tvOS-4BC51D.svg?style=flat">
<img src="https://img.shields.io/badge/license-MIT-3a3a3a">
</p>

Swift Package 📦 `SVProgressHUD` is a clean and easy-to-use HUD meant to display the progress of an ongoing task on iOS and tvOS. Refactored `Swift Package Manager` version of the [SVProgressHUD](https://github.com/SVProgressHUD/SVProgressHUD).

## Installation

Use `Swift Package Manager` to install.

## Usage

`SVProgressHUD` is created as a singleton (i.e. it doesn't need to be explicitly allocated and instantiated; you directly call `[SVProgressHUD method]`).

**Use `SVProgressHUD` wisely! Only use it if you absolutely need to perform a task before taking the user forward. Bad use case examples: pull to refresh, infinite scrolling, sending message.**

Using `SVProgressHUD` in your app will usually look as simple as this (using Grand Central Dispatch):

```objective-c
[SVProgressHUD show];
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // time-consuming task
    dispatch_async(dispatch_get_main_queue(), ^{
        [SVProgressHUD dismiss];
    });
});
```

### Showing the HUD

You can show the status of indeterminate tasks using one of the following:

```objective-c
+ (void)show;
+ (void)showWithStatus:(NSString*)string;
```

If you'd like the HUD to reflect the progress of a task, use one of these:

```objective-c
+ (void)showProgress:(CGFloat)progress;
+ (void)showProgress:(CGFloat)progress status:(NSString*)status;
```

### Dismissing the HUD

The HUD can be dismissed using:

```objective-c
+ (void)dismiss;
+ (void)dismissWithDelay:(NSTimeInterval)delay;
```

If you'd like to stack HUDs, you can balance out every show call using:

```
+ (void)popActivity;
```

The HUD will get dismissed once the popActivity calls will match the number of show calls.

### Flawless stacking

If you'd like to stack the same HUD on top of the previous one, as in case of serial requests to the remote presenting new HUD at the start of each one, stacking will produce a blinking effect. To achieve a flawless stacking without the HUD interface blinking use:

```
+ (void)setFlawlessStackingEnabled:(BOOL)isFlawlessStackingEnabled;
```

## Customization

`SVProgressHUD` can be customized via the following methods:

```objective-c
+ (void)setDefaultStyle:(SVProgressHUDStyle)style;                      // default is SVProgressHUDStyleLight
+ (void)setDefaultMaskType:(SVProgressHUDMaskType)maskType;             // default is SVProgressHUDMaskTypeNone
+ (void)setDefaultAnimationType:(SVProgressHUDAnimationType)type;       // default is SVProgressHUDAnimationTypeFlat
+ (void)setContainerView:(UIView*)containerView;                        // default is window level
+ (void)setMinimumSize:(CGSize)minimumSize;                             // default is CGSizeZero, can be used to avoid resizing
+ (void)setRingThickness:(CGFloat)width;                                // default is 2 pt
+ (void)setRingRadius:(CGFloat)radius;                                  // default is 18 pt
+ (void)setRingNoTextRadius:(CGFloat)radius;                            // default is 24 pt
+ (void)setCornerRadius:(CGFloat)cornerRadius;                          // default is 14 pt
+ (void)setBorderColor:(nonnull UIColor*)color;                         // default is nil
+ (void)setBorderWidth:(CGFloat)width;                                  // default is 0
+ (void)setFont:(UIFont*)font;                                          // default is [UIFont preferredFontForTextStyle:UIFontTextStyleSubheadline]
+ (void)setForegroundColor:(UIColor*)color;                             // default is [UIColor blackColor], only used for SVProgressHUDStyleCustom
+ (void)setForegroundImageColor:(nullable UIColor*)color;               // default is the same as foregroundColor
+ (void)setBackgroundColor:(UIColor*)color;                             // default is [UIColor whiteColor], only used for SVProgressHUDStyleCustom
+ (void)setHudViewCustomBlurEffect:(nullable UIBlurEffect*)blurEffect;  // default is nil, only used for SVProgressHUDStyleCustom, can be combined with backgroundColor
+ (void)setBackgroundLayerColor:(UIColor*)color;                        // default is [UIColor colorWithWhite:0 alpha:0.4], only used for SVProgressHUDMaskTypeCustom
+ (void)setImageViewSize:(CGSize)size;                                  // default is 28x28 pt
+ (void)setShouldTintImages:(BOOL)shouldTintImages;                     // default is YES
+ (void)setViewForExtension:(UIView*)view;                              // default is nil, only used if #define SV_APP_EXTENSIONS is set
+ (void)setGraceTimeInterval:(NSTimeInterval)interval;                  // default is 0 seconds
+ (void)setMinimumDismissTimeInterval:(NSTimeInterval)interval;         // default is 5.0 seconds
+ (void)setMaximumDismissTimeInterval:(NSTimeInterval)interval;         // default is infinite
+ (void)setFadeInAnimationDuration:(NSTimeInterval)duration;            // default is 0.15 seconds
+ (void)setFadeOutAnimationDuration:(NSTimeInterval)duration;           // default is 0.15 seconds
+ (void)setMaxSupportedWindowLevel:(UIWindowLevel)windowLevel;          // default is UIWindowLevelNormal
+ (void)setFlawlessStackingEnabled:(BOOL)isFlawlessStackingEnabled;     // default is NO
+ (void)setHapticsEnabled:(BOOL)hapticsEnabled;                         // default is NO
+ (void)setMotionEffectEnabled:(BOOL)motionEffectEnabled;               // default is YES
```

Additionally `SVProgressHUD` supports the `UIAppearance` protocol for most of the above methods.

### Hint

As standard `SVProgressHUD` offers two preconfigured styles:

* `SVProgressHUDStyleLight`: White background with black spinner and text
* `SVProgressHUDStyleDark`: Black background with white spinner and text

If you want to use custom colors use `setForegroundColor` and `setBackgroundColor:`. These implicitly set the HUD's style to `SVProgressHUDStyleCustom`.

## Haptic Feedback

For users with newer devices (starting with the iPhone 7), `SVProgressHUD` can automatically trigger haptic feedback depending on which HUD is being displayed. The feedback maps as follows:

* `showSuccessWithStatus:` <-> `UINotificationFeedbackTypeSuccess`
* `showInfoWithStatus:` <-> `UINotificationFeedbackTypeWarning`
* `showErrorWithStatus:` <-> `UINotificationFeedbackTypeError`

To enable this functionality, use `setHapticsEnabled:`.

Users with devices prior to iPhone 7 will have no change in functionality.

## Notifications

`SVProgressHUD` posts four notifications via `NSNotificationCenter` in response to being shown/dismissed:
* `SVProgressHUDWillAppearNotification` when the show animation starts
* `SVProgressHUDDidAppearNotification` when the show animation completes
* `SVProgressHUDWillDisappearNotification` when the dismiss animation starts
* `SVProgressHUDDidDisappearNotification` when the dismiss animation completes

Each notification passes a `userInfo` dictionary holding the HUD's status string (if any), retrievable via `SVProgressHUDStatusUserInfoKey`.

`SVProgressHUD` also posts `SVProgressHUDDidReceiveTouchEventNotification` when users touch on the overall screen or `SVProgressHUDDidTouchDownInsideNotification` when a user touches on the HUD directly. For this notifications `userInfo` is not passed but the object parameter contains the `UIEvent` that related to the touch.

## App Extensions

When using `SVProgressHUD` in an App Extension, `#define SV_APP_EXTENSIONS` to avoid using unavailable APIs. Additionally call `setViewForExtension:` from your extensions view controller with `self.view`.

## License

`SVProgressHUD` is distributed under the terms and conditions of the [MIT license](https://github.com/SVProgressHUD/SVProgressHUD/blob/master/LICENSE).

## Credits

`SVProgressHUD` is brought to you by [Sam Vermette](http://samvermette.com), [Tobias Tiemerding](http://tiemerding.com) and [contributors to the project](https://github.com/SVProgressHUD/SVProgressHUD/contributors). Refactored and adapted for use with the `Swift Package Manager` by [Vitalis Gkirsas](https://github.com/epitonium). If you're using `SVProgressHUD` in your project, attribution would be very appreciated.
