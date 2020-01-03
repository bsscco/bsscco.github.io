---
title: Appsflyer 어트리뷰션 모델에서 헷갈리는 것들 정리
date: 2018-11-21 00:00:00
tags: [appsflyer]
---
  
![attribution-model](https://support.appsflyer.com/hc/article_attachments/360000998949/user_journey.png)
참고 : https://support.appsflyer.com/hc/en-us/articles/207447053-What-is-AppsFlyer-s-Attribution-Model-

#### New-Attribution 구분
- 이미 특정 media_source로 New-attribution된 사용자는 다른 어떤 media_source에도 New-attribution으로 인식되지 않습니다.  

## Attribution Lookback window
#### View-through lookback window(1-7days)
- 사용자가 앱스플라이어를 통한 광고를 보고, 정해진 View-through lookback window 이내에 클릭 없이 앱을 설치한 경우 New-attribution으로 인정합니다.
- window 기본값은 1day
- onelink에서 설정할 수 있는 값은 아니고, 외부 partners setting에서 파트너마다 설정할 수 있습니다.

#### Click-through lookback window(1-30days)
- 사용자가 앱스플라이어 링크 또는 광고를 클릭하고, 정해진 Click-through lookback window 이내에 앱을 설치한 경우 New-attribution으로 인정합니다.
- window 기본값은 7days
- Link Management에서 Click-through window 값을 설정할 수 있습니다.
![click-through-window-setting](/2018-11-21/click-through-window-setting.png)

## Retargeting
#### Re-attribution window(1-24months), Re-installs, Re-attribution
- 사용자가 앱설치 후 정해진 Re-attribution window 이내에 재설치하면 Re-installs로 간주하고 Re-attribution으로 인정합니다.
- window 기본값은 90days
- App Setting에서 Re-attribution window 값을 설정할 수 있습니다.
![re-attribution-window](https://support.appsflyer.com/hc/article_attachments/115008565543/Attribution_Window.png)

#### Re-engagement
- 사용자가 앱설치 후 앱을 제거하지 않은 채로, 정해진 Re-attribution window 이내에 retargeting-campaign(view or click)을 경험하면 Re-engagement로 인정합니다.

#### Re-engagement window
- Re-engagement 캠페인의 이벤트 수집 기간입니다.
- window 기본값은 30days