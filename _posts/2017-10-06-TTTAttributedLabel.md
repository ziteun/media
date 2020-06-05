---
layout: post
title: "TTTAttributedLabel"
excerpt: "Lable에 링크"
categories: [objective-c]
modified: 2017-10-06
comments: true
---


[TTTAttributedLabel](https://github.com/TTTAttributedLabel/TTTAttributedLabel)

UILabel에 링크를 넣을 수 있다.

NSString은 당연히 되고, 각종 느낌?!들이 들어가는 NSAttributedString도 가능!

아래는 예제이다.


```

@implementation TTTAttributedLabelExam

- (void)viewDidLoad {
	[super viewDidLoad];

    TTTAttributedLabel *label = [[TTTAttributedLabel alloc] initWithFrame:CGRectZero];
    label.enabledTextCheckingTypes = NSTextCheckingTypeLink; 
    // Automatically detect links when the label text is subsequently changed
    label.delegate = self; 
    // Delegate methods are called when the user taps on a link (see `TTTAttributedLabelDelegate` protocol)

    label.text = @"Fork me on GitHub! (https://github.com/mattt/TTTAttributedLabel/)"; 
    // Repository URL will be automatically detected and linked

    NSRange range = [label.text rangeOfString:@"me"];
    [label addLinkToURL:[NSURL URLWithString:@"http://github.com/mattt/"] withRange:range]; 
    // Embedding a custom link in a substring
}

@end
```


위와 같은 경우라면 Fork me on GitHub라는 텍스트를 label에 지정했다.

당연히 label은 TTTAttributedlabel로 정의.

NSRange로 me 라는 단어를 range만큼 링크로 지정된다.

어플 사용시 쉽게 볼 수 있는 것으로 응용하자면

로그인이나 회원가입 하단에 '회원가입 시 약관에 동의하는 것으로 압니다.' 라는 레이블을 하단에 배치하고 

NSRange range = [label.text rangeOfString:@"약관"]; 에서 약관에 링크를 걸면 된다.

물론 하이퍼텍스트링크도 되지만 모달뷰로 띄울 수도 있다.

은근히 유용하게 쓰인다. ㅋㅅㅋ