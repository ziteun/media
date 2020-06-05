---
layout: post
title: "Singleton pattern"
excerpt: "싱글톤 패턴"
categories: [objective-c]
modified: 2018-02-11
comments: true
---

앱개발을 싱글톤 패턴은 자주 사용했다.

개발하는 김에 블로그에도 포스팅 한다.

prefix.pch 에 Sington을 임포트 시켜놓고 쓰면 언제든 필요할 떄 불러올 수 있어서 편리하다.


```
@interface AppDataModel : NSObject

+ (AppDataModel *)sharedDataModel;

@property (nonatomic, strong) NSString *userKey;

```

헤더파일에 위와 같은 형식으로 넣어 놓고,


```

+ (AppDataModel *)sharedDataModel {
    static AppDataModel *shared = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        shared = [[AppDataModel alloc] init];
    });
    return shared;
}

- (instancetype)init {
    self = [super init];

    if (self) {
        
    }

    return self;
}

```


```
[AppDataModel shareDataModel].userKey = @"someKey";
```

위와 같은 형식으로 어떤 키를 넣어놓고 쓸 수도 있고,

static으로 선언해 놓은 sharedDataModel을 초기화시킬 때 데이터를 넣어줄 수도 있다.

dispatch_once_t 를 쓰면 앱 라이프사이클에 따라서 단 한 번만 실행되는데 

회원 정보와 같은 데이터들을 처음 앱이 로드될 때 불러와 놓고 

필요할 때마다 가져다 쓸 수 있어서 유용하다.