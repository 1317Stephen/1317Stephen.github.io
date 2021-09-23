---
# layout: posts
title: "Swift Error Note"
date: 2021-09-09 10:44:40 +0900
categories:
  - Swift
tags:
  - Swift
  - iOS
excerpt: "This note summarizes the errors that occurred during the development of Swift."
toc: true

init_error:
  - url: /assets/images/swift-error/error_init.png
    image_path: assets/images/swift-error/error_init.png
    alt: "init_error"

---

## Type has no member
- Search:
    - [Swift - Type has no member](https://stackoverflow.com/questions/37355179/swift-type-has-no-member)
    - [Type has no member? Swift](https://developer.apple.com/forums/thread/655666)

  > This problem usually occurs in the module import error: wrong variable name, etc... 

- Cause: In my case, the problem is that the error is in the model class and I tried to use inside variable.
    
- Result: Fixed the error in the model class.


### Similar Error
- Error msg: 
  > Value of type 'UIViewController' has no member 'visibleViewContoller'
- Search:
  - [Receiving error: Value of type 'UIViewController' has no member 'openViewControllerBasedOnIdentifier'](https://stackoverflow.com/questions/46039287/receiving-error-value-of-type-uiviewcontroller-has-no-member-openviewcontrol)
  - [Value of type UIViewController has no member topViewController after Xcode 7 update](https://stackoverflow.com/questions/32815337/value-of-type-uiviewcontroller-has-no-member-topviewcontroller-after-xcode-7-upd)



## forUndefinedKey, NSUnknownKeyException
- Error msg:
> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key f."

- Search:
  - [ios NSUnknownKeyException](https://m.blog.naver.com/akj61300/220063207476)
- Cause: Referenced the deleted IBOulet in Storyboard.
- Result: Implemented the IBOulet in source code.

## git fail to commit files: '/newfolder' didnot match any files(s) known to git
- Error msg:
> git fail to commit files.   
> error: did not match any file(s) known to...

- Search:
	- [git push 오류 error: pathspec '' did not match any file(s) known to git](https://rrecoder.tistory.com/88)
	- [error: pathspec did not match any file(s) known to git](https://fomaios.tistory.com/entry/해결법-error-pathspec-did-not-match-any-files-known-to-git?category=857760)
	- [Xcode - error: pathspec '...' did not match any file(s) known to git
](https://stackoverflow.com/questions/27325747/xcode-error-pathspec-did-not-match-any-files-known-to-git)

- Cause: Mackbook/OSX cannot commit the chaning of foldername.
- Result: Git commit with console.

## 'self' parameter (inside struct init)
- Error msg: 
> 1. Escaping closure captures mutating 'self' parameter   
> 2. 'self' used before all stored properties are initialized   
{% include gallery id="init_error" caption="error in init method" %}

- Search: 
	- [Swift 5 : What's 'Escaping closure captures mutating 'self' parameter' and how to fix it
](https://stackoverflow.com/questions/58327013/swift-5-whats-escaping-closure-captures-mutating-self-parameter-and-how-t)
- Cuase: Trying to pass to the value type(structure).
- Result: Change the struct to the class or separate the method.


## Crash: NSInvalidArgumentException
<details>
  <summary>Error message</summary>
{% highlight bash %}
  2021-08-05 21:55:52.203428+0900 SmokingArea[8667:1145459] *** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'Invalid Region <center:+37.29994550, +126.89833000 span:+189.78684392, +130.01675267>'   
*** First throw call stack:   
(   
	0   CoreFoundation                      0x00007fff2041daf2 __exceptionPreprocess + 242   
	1   libobjc.A.dylib                     0x00007fff20177e78 objc_exception_throw + 48   
	2   CoreFoundation                      0x00007fff2041d793 -[NSException init] + 0   
	3   MapKit                              0x00007fff2f6efcc8 -[MKMapView setRegion:animated:] + 687   
	4   SmokingArea                         0x0000000108c66fa8 $s11SmokingArea14ViewControllerC15mapScaleStepperyySo9UIStepperCF + 1256   
	5   SmokingArea                         0x0000000108c67034 $s11SmokingArea14ViewControllerC15mapScaleStepperyySo9UIStepperCFTo + 68    
	6   UIKitCore                           0x00007fff2467b62e -[UIApplication sendAction:to:from:forEvent:] + 83    
	7   UIKitCore                           0x00007fff23fa496c -[UIControl sendAction:to:forEvent:] + 223    
	8   UIKitCore                           0x00007fff23fa4c8f -[UIControl     _sendActionsForEvents:withEvent:] + 332   
	9   UIKitCore                           0x00007fff24a68f0a -[UIStepperHorizontalVisualElement _updateCount:] + 367   
	10  UIKitCore                           0x00007fff24a68be0 -[UIStepperHorizontalVisualElement endTrackingWithTouch:withEvent:] + 33   
	11  UIKitCore                           0x00007fff23fd0750 -[UIStepper endTrackingWithTouch:withEvent:] + 95   
	12  UIKitCore                           0x00007fff23fa3549 -[UIControl touchesEnded:withEvent:] + 453   
	13  UIKitCore                           0x00007fff246b7d6a -[UIWindow _sendTouchesForEvent:] + 1287   
	14  UIKitCore                           0x00007fff246b9be3 -[UIWindow sendEvent:] + 4774   
	15  UIKitCore                           0x00007fff246938f6 -[UIApplication sendEvent:] + 633   
	16  UIKitCore                           0x00007fff2472439c __processEventQueue + 13895   
	17  UIKitCore                           0x00007fff2471ad0f __eventFetcherSourceCallback + 104   
	18  CoreFoundation                      0x00007fff2038c37a __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__ + 17   
	19  CoreFoundation                      0x00007fff2038c272 __CFRunLoopDoSource0 + 180   
	20  CoreFoundation                      0x00007fff2038b754 __CFRunLoopDoSources0 + 248   
	21  CoreFoundation                      0x00007fff20385f1f __CFRunLoopRun + 878   
	22  CoreFoundation                      0x00007fff203856c6 CFRunLoopRunSpecific + 567   
	23  GraphicsServices                    0x00007fff2b76adb3 GSEventRunModal + 139   
	24  UIKitCore                           0x00007fff24675187 -[UIApplication _run] + 912   
	25  UIKitCore                           0x00007fff2467a038 UIApplicationMain + 101   
	26  libswiftUIKit.dylib                 0x00007fff541545f2 $s5UIKit17UIApplicationMainys5Int32VAD_SpySpys4Int8VGGSgSSSgAJtF + 98   
	27  SmokingArea                         0x0000000108c6c62a $sSo21UIApplicationDelegateP5UIKitE4mainyyFZ + 122   
	28  SmokingArea                         0x0000000108c6c59e $s11SmokingArea11AppDelegateC5$mainyyFZ + 46   
	29  SmokingArea                         0x0000000108c6c679 main + 41   
	30  libdyld.dylib                       0x00007fff20256409 start + 1   
	31  ???                                 0x0000000000000001 0x0 + 1   
)   
libc++abi.dylib: terminating with uncaught exception of type NSException   
*** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'Invalid Region <center:+37.29994550, +126.89833000 span:+189.78684392, +130.01675267>'   
terminating with uncaught exception of type NSException    
CoreSimulator 732.18.0.2 - Device: iPhone 12 Pro Max (C3DAD0D5-84C9-4EA4-8E41-76B189B5C107) - Runtime: iOS 14.2 (18B79) - DeviceType: iPhone 12 Pro Max    
{% endhighlight %}
</details>

- Search:

- Cause:
- Result: