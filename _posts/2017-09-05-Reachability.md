---
layout: post
title: "Reachability 네트워크 상태 체크"
excerpt: "실시간으로 네트워크 연결 상태 감지"
categories: [objective-c]
modified: 2017-09-05
comments: true
---


[Reachability](https://developer.apple.com/library/content/samplecode/Reachability/Introduction/Intro.html)

Reachability링크를 들어가서 Download Sample Code를 눌러 다운받는다.

그 후 .h 헤더파일과 .m 구현부 파일을 프로젝트에 추가한 뒤 임포트한다.

```
@property (nonatomic) Reachability *hostReachability;
@property (nonatomic) Reachability *internetReachability;

...

[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(reachabilityChanged:) name:kReachabilityChangedNotification object:nil];

...

- (void)reachabilityChanged:(NSNotification *)note {
	Reachability* curReach = [note object];
	NSParameterAssert([curReach isKindOfClass:[Reachability class]]);
	[self updateInterfaceWithReachability:curReach];
}

- (void)updateInterfaceWithReachability:(Reachability *)reachability {
    if (reachability == self.hostReachability) {
		[self configureTextField:self.remoteHostStatusField imageView:self.remoteHostImageView reachability:reachability];
        NetworkStatus netStatus = [reachability currentReachabilityStatus];
        BOOL connectionRequired = [reachability connectionRequired];

        self.summaryLabel.hidden = (netStatus != ReachableViaWWAN);
        NSString* baseLabelText = @"";
        
        if (connectionRequired) {
            baseLabelText = NSLocalizedString(@"Cellular data network is available.\nInternet traffic will be routed through it after a connection is established.", @"Reachability text if a connection is required");
        } else {
            baseLabelText = NSLocalizedString(@"Cellular data network is active.\nInternet traffic will be routed through it.", @"Reachability text if a connection is not required");
        }
        self.summaryLabel.text = baseLabelText;
    }
	if (reachability == self.internetReachability) {
		[self configureTextField:self.internetConnectionStatusField imageView:self.internetConnectionImageView reachability:reachability];
	}
}

- (void)configureTextField:(UITextField *)textField imageView:(UIImageView *)imageView reachability:(Reachability *)reachability {
    NetworkStatus netStatus = [reachability currentReachabilityStatus];
    BOOL connectionRequired = [reachability connectionRequired];
    NSString* statusString = @"";
    
    switch (netStatus) {
        case NotReachable: {
            statusString = NSLocalizedString(@"Access Not Available", @"Text field text for access is not available");
            imageView.image = [UIImage imageNamed:@"stop-32.png"] ;
            /*
             Minor interface detail- connectionRequired may return YES even when the host is unreachable. We cover that up here...
             */
            connectionRequired = NO;
            break;
        }
        case ReachableViaWWAN: {
            statusString = NSLocalizedString(@"Reachable WWAN", @"");
            imageView.image = [UIImage imageNamed:@"WWAN5.png"];
            break;
        }
        case ReachableViaWiFi: {
            statusString= NSLocalizedString(@"Reachable WiFi", @"");
            imageView.image = [UIImage imageNamed:@"Airport.png"];
            break;
        }
    }
    
    if (connectionRequired) {
        NSString *connectionRequiredFormatString = NSLocalizedString(@"%@, Connection Required", @"Concatenation of status string with connection requirement");
        statusString= [NSString stringWithFormat:connectionRequiredFormatString, statusString];
    }
    textField.text= statusString;
}
```


뭐 이미 다 샘플코드에 간단히 구현되어 있어서 설명도 필요없다.


응용해서 사용하자면 인터넷 디스커넥트마다 노티피케이션을 날려서 


뷰마다 다르게 사용할 수도 있다.