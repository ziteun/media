---
layout: post
title: "blocks, delegate, notification"
excerpt: "blocks, delegate, notification 사용"
categories: [objective-c]
modified: 2017-08-29
comments: true
---

코딩하다보면 빈번하게 많이 쓰이는 것들이다.

blocks

블록코딩은 비동기처리 할 때 쓴다.

기본적으로 쓰는 선언방법은

{% highlight c %}
//^이 블록 기호
^(prameter) {
​	//code
}
{% endhighlight %}



애플 가이드 샘플코드를 보자.

[Apple Blocks Link](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Blocks/Articles/00_Introduction.html)

위의 링크버튼을 누르면 넘어간다.

Getting Started with Blocks 누르면 선언과 블록 사용이 나온다.

{% highlight c %}
int multiplier = 7;
int (^myBlock)(int) = ^(int num) {
​	return num * multiplier;
}
{% endhighlight %}

myBlock을 int로 리턴받는다.

문서보면 자세히 나와있어서 뭐 보면 금방 사용 가능함.

Blocks, Delegate, Notification 사용하면서 여기 포스트에서 

말하고 싶은 것은 이 부분이다.

[StackOverFlowLink](https://stackoverflow.com/questions/10387039/how-can-i-detect-the-dismissal-of-a-modal-view-controller-in-the-parent-view-con/10387503#10387503)

In *MainViewController.m*

```
SecondViewController* svc = [[SecondViewController alloc] init];
svc.didDismiss = ^(NSString *data) {
    // this method gets called in MainVC when your SecondVC is dismissed
    NSLog(@"Dismissed SecondViewController");
};

[self presentViewController:svc animated:YES completion:nil];
```

In *SecondViewController.h*

```
@interface MainViewController : UIViewController
    @property (nonatomic, copy) void (^didDismiss)(NSString *data);
    // ... other properties
@end
```

In *SecondViewController.m*

```
- (IBAction)close:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];

    if (self.didDismiss) 
        self.didDismiss(@"some extra data");
}
```

계속 공부하다보면 계속 공부할 것이 나온다.

이렇게 짬에서 나오는 바이브...

더 많이 알고싶다.



링크에 들어가보면 Delegation, Notification 등을 활용하여 

네비게이션을 다루는 방법들이 있다.

잊고싶지 않아서 복사해둔다.

/

*This answer was rewritten/expanded to explain the 3 most important approaches (@galambalazs)*

## 1. Blocks

The simplest approach is using a callback [`block`](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html). This is good if you only have **one listener** (the parent view controller) interested in the dismissal. You may even pass some data with the event.

In *MainViewController.m*

```
SecondViewController* svc = [[SecondViewController alloc] init];
svc.didDismiss = ^(NSString *data) {
    // this method gets called in MainVC when your SecondVC is dismissed
    NSLog(@"Dismissed SecondViewController");
};

[self presentViewController:svc animated:YES completion:nil];
```

In *SecondViewController.h*

```
@interface MainViewController : UIViewController
    @property (nonatomic, copy) void (^didDismiss)(NSString *data);
    // ... other properties
@end
```

In *SecondViewController.m*

```
- (IBAction)close:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];

    if (self.didDismiss) 
        self.didDismiss(@"some extra data");
}
```

------

## 2. Delegation

[**Delegation**](https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html#//apple_ref/doc/uid/TP40008195-CH14) is the recommended pattern by Apple:

> **Dismissing a Presented View Controller**
>
> If the presented view controller must return data to the presenting view controller, use the [delegation](https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html#//apple_ref/doc/uid/TP40008195-CH14) design pattern to facilitate the transfer. Delegation makes it easier to reuse view controllers in different parts of your app. With delegation, the presented view controller stores a reference to a delegate object that implements methods from a formal [protocol](https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Protocol.html#//apple_ref/doc/uid/TP40008195-CH45). As it gathers results, the presented view controller calls those methods on its delegate. In a typical implementation, the presenting view controller makes itself the delegate of its presented view controller.

**MainViewController**

In *MainViewController.h*

```
@interface MainViewController : UIViewController <SecondViewControllerDelegate>
    - (void)didDismissViewController:(UIViewController*)vc;
    // ... properties
@end
```

Somewhere in *MainViewController.m* (presenting)

```
SecondViewController* svc = [[SecondViewController alloc] init];
svc.delegate = self;
[self presentViewController:svc animated:YES completion:nil];
```

Somewhere else in *MainViewController.m* (being told about the dismissal)

```
- (void)didDismissViewController:(UIViewController*)vc {
    // this method gets called in MainVC when your SecondVC is dismissed
    NSLog(@"Dismissed SecondViewController");
}
```

**SecondViewController**

In *SecondViewController.h*

```
@protocol SecondViewControllerDelegate <NSObject>
    - (void)didDismissViewController:(UIViewController*)vc;
@end

@interface SecondViewController : UIViewController
    @property (nonatomic, weak) id<SecondViewControllerDelegate> delegate;
    // ... other properties
@end
```

Somewhere in *SecondViewController.m*

```
[self.delegate myActionFromViewController:self];
[self dismissViewControllerAnimated:YES completion:nil];
```

*(note: the protocol with didDismissViewController: method could be reused throughout your app)*

------

## 3. Notifications

Another solution is sending an [**NSNotification**](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/). This is a valid approach as well, it might be easier than delegation in case you only want to *notify* about the dismissal without passing much data. But it's main use case is when you want **multiple listeners** for the dismissal event (other than just the parent view controller).

But make sure to **always** remove yourself from *NSNotificationCentre* after you are done! Otherwise you risk of crashing by being called for notifications even after you are deallocated.  *[editor's note]*

In *MainViewController.m*

```
- (IBAction)showSecondViewController:(id)sender {
    SecondViewController *secondVC = [[SecondViewController alloc] init];
    [self presentViewController:secondVC animated:YES completion:nil];

    // Set self to listen for the message "SecondViewControllerDismissed"
    // and run a method when this message is detected
    [[NSNotificationCenter defaultCenter] 
     addObserver:self
     selector:@selector(didDismissSecondViewController)
     name:@"SecondViewControllerDismissed"
     object:nil];
}

- (void)dealloc {
    // simply unsubscribe from *all* notifications upon being deallocated
    [[NSNotificationCenter defaultCenter] removeObserver:self];
} 

- (void)didDismissSecondViewController {
    // this method gets called in MainVC when your SecondVC is dismissed
    NSLog(@"Dismissed SecondViewController");
}
```

In *SecondViewController.m*

```
- (IBAction)close:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];

    // This sends a message through the NSNotificationCenter 
    // to any listeners for "SecondViewControllerDismissed"
    [[NSNotificationCenter defaultCenter] 
     postNotificationName:@"SecondViewControllerDismissed" 
     object:nil userInfo:nil];
}
```

Hope this helps!

/


blocks, delegation, notification

차후에 각각 사용했었던 코드들을 적어놔야겠다.

그리고 더 자세히 포스팅 해야겠다.

그런데 사용해보면 간단해서 뭐 크게 적을게 있겠냐만은 

또 여러가지 방식으로 사용할 수 있으니까 더 자세히 적어봐야겠다.