---
layout: post
title: "LaunchScreen Loading Delay"
excerpt: "런치스크린을 로딩이 다 될 때 사라지게 할 수 있다."
categories: [objective-c]
modified: 2017-09-18
comments: true
---


[LaunchScreenViewController](https://github.com/k06a/LaunchScreenViewController)

LaunchScreenViewController.h 와 LaunchScreenViewController.m 구현부 파일을 프로젝트에 추가시킨다.

```
@implementation LaunchScreen

- (void)viewDidLoad {
	[super viewDidLoad];

    //ViewController에서 스토리보드의 segue_screensaver 세그를 만나면 뷰가 넘어간다.
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        [self performSegueWithIdentifier:@"segue_screensaver" sender:nil];
    });
}

...

- (void)viewDidLoad {
	[super viewDidLoad];
    //segue_screensaver 세그로 넘어갔던 뷰에서 할 거 다 하고 디스미스 된다.
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        [self dismissViewControllerAnimated:YES completion:nil];
    });
}

@end
```


스토리보드에서 처음 실행되는 ViewController가 있다면 바로 segue_screensaver에 해당되는 뷰를 보여준다.

그 뷰가 런치스크린이 되고 런치스크린 구현부에서 딜레이가 걸리는 일을 다 끝내면 디스미스 시키면 된다.

런치스크린을 이런식으로 간단히 로딩뷰를 구현할 수 있다.

안드로이드는 스플레시 스크린을 간단히 딜레이시킬 수 있지만 iOS는 그게 안된다.

LaunchScreen.storyboard에 커스텀 클래스를 추가하면 

'Launch screens may not set custom classnames' 에러가 나면서 빌드가 안된다.

물론 다른 방법으로 BaseViewController를 상속받아서 런치스크린을 할 수도 있지만 

단순히 로딩이 끝날 때 디스미스 시키면 될 것을 간단히 구현하고 싶을 떄 사용하면 좋을 것 같아서 써봤는데 괜찮은 것 같다.