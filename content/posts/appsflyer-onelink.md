---
title: Appsflyer 원링크 파라메터 정리
date: 2018-11-21 00:00:00
tags: [appsflyer, onelink]
---

ex) 
```
https://ohouse.onelink.me/1234567?pid=Social%20Facebook&c=bssccocampaign&af_channel=bssccochannel&is_retargeting=true&af_click_lookback=15d&af_dp=ohouseapp%3A%2F%2Fohou.se%2Fcards%2F1234&af_web_dp=https%3A%2F%2Fohou.se%2Fcards%2F1234
```
  
#### pid
media_source

#### c
campagin

#### af_channel
channel

#### is_retargeting
Re-attribution, Re-engagement 트래킹을 허용할 것인가?

true일 경우에 New-installs라도 New-Attribution으로 인식 안 됨.

#### af_click_lookback
Click-through lookback window값

#### af_view_lookback
View-through lookback window값

#### af_dp
네이티브 앱용 딥링크

#### af_web_dp
웹용 딥링크

#### af_reengagement_window
Re-engagement window값