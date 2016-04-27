---
layout: default
title: UIKit-ViewWillAppear
---

  Just record something unexpected happenned when using UIKit.
 
ViewWillAppear/Disappear
-----
  Occasionally we will add our logic during `viewWillAppear` like adding a observer & remove it in `viewWillDisappear`.

###Let's check this out with a simple example

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


  The code above will log out like this.

    2016-04-27 10:49:46.694 TestMe[67943:4951489] vc1 viewWillAppear
    2016-04-27 10:49:51.692 TestMe[67943:4951489] vc2 viewWillAppear
    2016-04-27 10:49:56.693 TestMe[67943:4951489] vc3 viewWillAppear


###Sometimes we got code like this, in vc3

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.
    
        self.vc4 = [[UIViewController alloc] init];
        [self.view addSubview:self.vc4.view];
    }


   But vc4 `viewWillAppear` will never log out!
   

###We can have a little more test

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.
    
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            self.vc4 = [[UIViewController alloc] init];
            [self.view addSubview:self.vc4.view];
        });
    }


    Now we got the log!


  So if you try to addSubview before parentViewController 'viewWillAppear', then you won't got the 'viewWillAppear' callback.

 
### Shared by
 Ivan Chan (airivan@hotmail.com)
