---
layout: default
title: UIKit-ViewAppear/Disappear
---

  Sometimes we are confused about the firing of `viewAppear/Disappear`.
  
  Basically, they are fired when super-view is on-screen/off-screen. (You can set a breakpoint to check the stack)
  
  What's more, we feel like some ViewControllers just don't get the `viewAppear/Disappear` message, we can find out why.
    
 
Don't AddSubview Before ParentViewController Appear ?
-----
  Occasionally we have logics in `viewDidAppear` like adding a observer & remove it in `viewDidDisappear`.
  
  ** Don't do it in WillAppear/WillDisappear, because they are not pairing fired in some circumstance.**
  
  **e.g. During interactive transitioning**

### Let's check this out with a simple example

    UIViewController *vc1 = [[UIViewController alloc] init];
    UIViewController *vc2 = [[UIViewController alloc] init];
    UIViewController *vc3 = [[UIViewController alloc] init];

    UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:vc1];
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        [navigationController pushViewController:vc2 animated:YES];
    });
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        [navigationController pushViewController:vc3 animated:YES];
    });


  `The code above will log out like this.`

    2016-04-27 10:49:46.694 TestMe[67943:4951489] vc1 viewWillAppear
    2016-04-27 10:49:51.692 TestMe[67943:4951489] vc2 viewWillAppear
    2016-04-27 10:49:56.693 TestMe[67943:4951489] vc3 viewWillAppear


### Sometimes we got codes like this, in vc3

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.
    
        self.vc4 = [[UIViewController alloc] init];
        [self.view addSubview:self.vc4.view];
    }


   But vc4 `viewWillAppear` will NOT be fired!
   

### We can try again with a delay & see what happens

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.
    
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            self.vc4 = [[UIViewController alloc] init];
            [self.view addSubview:self.vc4.view];
        });
    }


  **Now we got the callback!**
  
  So if you try to addSubview before parentViewController `viewWillAppear`, then you won't got the `viewWillAppear` callback.
  
 
Sometims we might solve it like this
-----

```
- (void)viewDidLoad {
    [super viewDidLoad];

    self.vc4 = [[UIViewController alloc] init];
    [self.view addSubview:self.vc4.view];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];

    [self.vc4 viewWillAppear:animated];
}

```

We won't get the viewWillAppear from iOS, so sometimes we call it ourseleves, simple and problem solved.

But it's crazy & stupid to do that if there is more than 3-level views!

### The right way to achieve what we want


```
- (void)viewDidLoad {
    [super viewDidLoad];

    self.vc4 = [[UIViewController alloc] init];
    [self.view addSubview:self.vc4.view];
    
    // We have one more step here
    [self addChildViewController:self.vc4]
}

```

  **This time we also get the appear callback!**
  
  Now we know that iOS connect ViewControllers with `addChildViewController` & `removeParentViewController`, so the appear/disappear message can pass from parent to sons.
  
  It's very important for you when you assemble ViewControllers in a ParentViewController, if you don't connect them, you will miss some viewAppear/Disappear message when using TabBarController or NavigationController like this.
  
  
```
- navigation
    1. push A-VC                     // receive view appear
             |
             -- AA-VC                // NOT receive view appear
    2. push B-VC                     // receive view appear
             |  
        (B-VC addChildViewController: BB-VC)
             -- BB-VC                // receive view appear
    3. push C-VC                     // receive view appear
             

```

If you have customized your own TabBarController or NavigationController, don't forget to connect them. 

As last, remember to call `removeParentViewController` or it might cause a leak.
 
### Shared by
 Ivan Chan (aintivanc@icloud.com)
