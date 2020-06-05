---
layout: post
title: "Google Admob"
excerpt: "구글 애드몹"
categories: [objective-c]
modified: 2017-10-04
comments: true
---


[GoogleAdmob](https://developers.google.com/admob/ios/interstitial)

구글 애드몹을 전면 광고로 해 수익을 올릴 수 있다.

```
@property(nonatomic, strong) GADInterstitial *interstitial;

...

//
- (void)viewDidLoad {
    [super viewDidLoad];
    self.interstitial = [[GADInterstitial alloc] init];
    //애드몹 앱ID를 넣는다
    self.interstitial.adUnitID = @"ca-app-pub-8084139945896039/3483224303";
    //<delegate> 연결
    self.interstitial.delegate = self;
    //리퀘스트 로드를 날린다.
    [self.interstitial loadRequest:[GADRequest request]];

    // Assumes an image named "SplashImage" exists.
    self.imageView = [[UIImageView alloc] initWithImage:[UIImage  imageNamed:@"SplashImage"]];
    self.imageView.frame = self.view.frame;
    self.imageView.contentMode = UIViewContentModeScaleAspectFill;
    [self.view addSubview:self.imageView];
}

//리퀘스트 로드를 받은 후 광고를 받는다.
- (void)interstitialDidReceiveAd:(GADInterstitial *)interstitial {
    //로드 리퀘스트가 성공하면 모달로 광고를 띄운다.
    [self.interstitial presentFromRootViewController:self];
    NSLog(@"광고받음");
}

//광고 리퀘스트 실패시 실행
- (void)interstitial:(GADInterstitial *)interstitial
didFailToReceiveAdWithError:(GADRequestError *)error {
    [self.imageView removeFromSuperview];
    NSLog(@"Error");
}

//모달로 띄워진 광고가 종료되기 전에 실행된다.
- (void)interstitialWillDismissScreen:(GADInterstitial *)interstitial {
    [self.imageView removeFromSuperview];
    NSLog(@"광고디스미스 후 실행");

}
```


이렇게 간단하게 광고를 띄우고 수익을 낼 수 있다.

정확하게는 수익을 내는 방법이 쉬운거지 수익을 만드는게 쉬운건 아니다... 흑흑...