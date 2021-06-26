---
layout: post
title: 스프링 스케줄러 관련 내용 배포시 발생한 이슈
date: 2021-06-26 19:20:23 +0900
category: Java

---

## 스프링 스케줄러 관련 내용 배포시 발생한 이슈

> Scheduled annotation을 활용하여 메일 자동발송을 하는 로직을 배포했을 때, 두 번 발송되는 이슈 발생

##### 해결한 방법

```xml
<Host appBase="D:/DEV/WEBSERVICE" name="127.0.0.1:8080" deployOnStartup="false" autoDeploy="false">
	<Context docBase="WebContent" path="" reloadable="true" >
	</Context>
</Host>      
```

**Host 태그에서 `deployOnStartup` 과`autoDeploy` 속성 값을 false로 지정 **
