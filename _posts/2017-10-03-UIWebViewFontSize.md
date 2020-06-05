---
layout: post
title: "UIWebView Font Size"
excerpt: "웹뷰 폰트 사이즈"
categories: [objective-c]
modified: 2017-10-03
comments: true
---


[UIWebViewDelegate](https://developer.apple.com/documentation/uikit/uiwebviewdelegate)

- webView:shouldStartLoadWithRequest:navigationType:
- webViewDidStartLoad:
- webViewDidFinishLoad:
- webView:didFailLoadWithError:

웹뷰 델리게이트는 4개가 있음.


```
//웹뷰 로딩하기 전에 실행된다.
- webView:shouldStartLoadWithRequest:navigationType:
//웹뷰 로딩 후 시작된다.
- webViewDidStartLoad:
//웹뷰 로딩 끝난 다음에 시작된다. / HUD 로딩뷰 같은걸 여기에 넣어두면 될 듯.
- webViewDidFinishLoad:
//웹뷰 로딩 실패시
- webView:didFailLoadWithError:
```


webViewDidFinishLoad 쓰려면 <UIWebViewDelegate> 하고
self.webView.delegate = self; 하고 쓰면 됨.

```
@implementation WebView

- (void)webViewDidFinishLoad:(UIWebView *)webView {
    NSString *fontSize=@"13";
    NSString *jsString = [[NSString alloc] initWithFormat:
    @"document.getElementsByTagName('body')[0].style.webkitTextSizeAdjust= '%d%%'", [fontSize intValue]];
    [self.webView stringByEvaluatingJavaScriptFromString:jsString];
}

@end
```


이걸 쓴 이유는 내가 받은 json의 컨텐츠가 html 태그로 되어있는 상태였는데 그 상태에서 폰트만 키우고 싶었었다.

그래서 "<body>" 태그 안에 있는 텍스트들의 사이즈를 커스텀하고 싶었는데 잘 돌아간다.