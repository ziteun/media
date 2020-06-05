---
layout: post
title: "Toggle Button"
excerpt: "토글 버튼"
categories: [objective-c]
modified: 2017-09-25
comments: true
---


토글 버튼 간편하게 코딩.

뭐 

```
@implementation ToggleButton

- (void)viewDidLoad {
	[super viewDidLoad];

    UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
    [button addTarget:self action:@selector(switchToggled:)
    forControlEvents:UIControlEventTouchUpInside];
}

- (void)switchToggled:(id)sender {
    UISwitch *mySwitch = (UISwitch *)sender;
    if ([mySwitch isOn]) {
        NSLog(@"its on!");
    } else {
        NSLog(@"its off!");
    }
}

@end
```


처럼 할 수도 있고... 방법은 많다.

안드로이드는 기본으로 있지만 iOS는 없어서 이런식으로 만들어주는게 귀찮다.

더 확실하게 하는 방법으로는 if식을 Bool 값으로 구분하는 거다.

코딩 관성인지는 몰라도 불리언 값으로 구분하는게 마음편하다.