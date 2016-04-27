---
layout: default
title: ICPatternLock
---

  ICPatternLock is a screen lock for your app access. It's familiar to most people and intuitively clear, 
  because a pattern is much more easy to remember.
  What is more, it's also convenient for developers to use, of course, and to extend.
 
 ![](https://github.com/IvanChan/ICPatternLock/raw/master/demo.png) 
 
 Pod is supported, try to add this to your `Podfile` 
  ```
    pod 'ICPatternLock', :git => 'https://github.com/IvanChan/ICPatternLock.git'
  ```
 
Convenience Usage
-----
  If you don't care much about the view display and some interactions, just use the `ICPatternLockManager` to present the pattern
  lock and receive the result.
  Of cource you can cusomize your GUI(e.g. color & text & preference) in this way. 
  </br>All what you need to do is just run your logic in the callback block.
  </br>Check out the `Demo Project` to experience this.

###To configure your pattern at the first time
  ```
  if ([ICPatternLockManager isPatternSetted])
    {
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Hint"
                                                        message:@"Pattern already setted"
                                                       delegate:nil
                                              cancelButtonTitle:@"OK"
                                              otherButtonTitles:nil];
        [alert show];
    }
    else
    {
        [ICPatternLockManager showSettingPatternLock:self
                                            animated:YES
                                        successBlock:^(ICPatternLockViewController *controller){
            
                                            dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
                                                
                                                [controller dismissViewControllerAnimated:YES completion:nil];
                                            });
                                        }];
    }
  ```
###Verify the pattern you previously set
  ```
  [ICPatternLockManager showVerifyPatternLock:self
                                           animated:YES
                                     forgetPwdBlock:^(ICPatternLockViewController *controller){
                                         
                                         NSLog(@"OMG~ forgot my pattern!~");
                                     }
                                       successBlock:^(ICPatternLockViewController *controller){
                                           
                                           dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
                                               
                                               [controller dismissViewControllerAnimated:YES completion:nil];
                                           });
                                       }];
  ```
###Modify the pattern
  ```
        [ICPatternLockManager showModifyPatternLock:self
                                           animated:YES
                                       successBlock:^(ICPatternLockViewController *controller) {
                                           
                                           dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
                                               
                                               [controller dismissViewControllerAnimated:YES completion:nil];
                                           });
                                           
                                       }];
  ```
  
### Customize Resource & Preference
 Use api below to custom your own resource (text display & color) & preference (e.g. node size, key table)
 </br> All key is defined in `PLPreferencesKeyDef.h` `PLResourceTextKeyDef.h` `PLResourceColorKeyDef.h`
 </br> Change all the value or maybe just one of them if you like.
```
#pragma mark - Resource Bridge
+ (void)loadTextResource:(NSDictionary *)textInfo;
+ (void)loadColorResource:(NSDictionary *)colorInfo;

#pragma mark - Preferences
- (void)setPreferencesValue:(id)value forKey:(NSString *)key;

#pragma mark - PatternHandler
+ (void)setCustomizedPatternHandler:(id<ICPatternHandlerProtocol>)handler;
```

Flexible Usage
-----
 If you want to assemble this to your own logic, using `ICPatternLockViewController` directly, it has enough api for user.
 </br>What's more, you can use `ICPatternLockView` to achive other goal if you like, imagination has no end！
 
### Created by
 Ivan Chan (airivan@hotmail.com)
