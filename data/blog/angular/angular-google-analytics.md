---
title: 'Angular Google Analytics Traking'
date: '2022-01-02'
tags: ['web', 'angular', 'google analytics']
draft: false
summary: '구글 분석을 사용하기 위해서는 추척아이디가 포함된 스크립트를 index.html 페이지에 삽입만 하면 되기 때문에 아주 간단하게 연동을 할 수 있습니다.'
---

구글 분석을 사용하기 위해서는 추척아이디가 포함된 스크립트를 index.html 페이지에 삽입만 하면 되기 때문에 아주 간단하게 연동을 할 수 있습니다. 그러나 Angular와 같은 SPA방식은 단일페이지로 되어 있기 때문에 단순히 스크립트를 복사 붙여넣기 방식으로 만으로 우리가 원하는 분석데이터를 수집하기에는 아쉬움이 있습니다.

## 그럼 어떻게?

Angular는 단일페이지 애플리케이션 프레임워크입니다. 즉, 브라우저는 기술적으로 한 페이지만 로드하고 페이지가 변경되면 DOM은 프레임워크의 JavaScript 코드에 지정된 대로 페이지의 일부만 업데이트 합니다.

컴포넌트는 시각적인 퍼즐의 일부를 구성하는 것이고 특정 보기에서 다른 곳으로 이동할 수 있고 주소 표시줄의 링크가 일치하도록 동적으로 변경되지만 여전히 동일한 루트 페이지에 있습니다.

브라우저가 페이지를 아동해도 새로고침 없이 여전히 동일한 페이지 이기 때문에 Google Analytics 태크의 기본 구현은 이를 알지 못합니다.

따라서 라우터 이벤트를 통해 변경된 페이지 정보를 추가로 전달해 주어야 합니다.

## gtag.js

Google Analytics를 통한 추적에는 `ga` 및 `gtag`의 두 가지 버전이 있습니다. ga는 더 오래된 버전이며 Google에서는 대신 gtag.js 구현을 추진하고 있습니다.

시작하려면 Google Analytics 패널로 이동하여 복사하여 붙여넣기 코드를 찾아야 합니다. 트래킹ID가 포함된 코드를 복사하여 `<head>` 섹션에 넣어주면 됩니다.

데이터 스트림에서 스트림을 추가하면 그림과 같이 트래킹ID 및 HTML에 삽입할 script를 확인 할 수 있습니다.
![01](/posts/angular/image1.png)

## Router Events 정보 수집

이제 페이지 이동 정보를 수집하기 위해 별도의 코드를 작성해야 합니다.
Angular에는 특정 정보에 액세스하는 데 사용할 수 있는 몇 가지 이벤트 처리 방법이 있는데, 우리는 사용자가 탐색한 URL정보가 필요하기에 라우터 이벤트를 구독 할 것입니다.

이 정보를 추출하기 위해 `navigationEnd` 메소드를 사용하여 `urlAfterRedirects`라는 속성에 액세스할 수 있습니다. 이 속성에는 라우팅 URL의 `/` 부분이 포함됩니다.

코드 효율성을 위해 이 모든 것을 `app.component.ts` 파일에 배치할 수 있습니다. 이 파일은 모든 것의 최상위 수준에서 로드되는 첫 번째 파일이기 때문입니다.

추적을 더욱 강화하는 데 사용할 수 있는 다른 매개변수가 있습니다. 최신 매개변수 목록은 Google Analytics 추적 페이지 보기 문서 페이지에서 확인할 수 있습니다. (https://developers.google.com/analytics/devguides/collection/gtagjs/pages)

```typescript
...
import{Router, NavigationEnd} from '@angular/router';
...
declare let gtag: Function;
...
export class AppComponent {
  constructor(public router: Router){
      this.router.events.subscribe(event => {
         if(event instanceof NavigationEnd){
             gtag('config', 'x-xxxxxxxxxx',
                   {
                     'page_path': event.urlAfterRedirects
                   }
                  );
          }
       }
    )}
}
```

## Event Tracking

이벤트 추적은 Analytics 통계에 또 다른 데이터 계층을 추가합니다. 이벤트 트래킹은 원하는 이벤트가 발생시 수집될 수 있도록 해야 하기 때문에 Angular 서비스를 만들고 구성 요소에서 사용할 것입니다.

이렇게 하려면 CLI를 사용하여 서비스를 생성한 다음 이를 공급자 중 하나로 `app.module.ts` 파일에 추가할 수 있습니다.

```
ng generate s GoogleAnalytics
```

`app.module.ts`

```
...
import {GoogleAnalyticsService} from './google-analytics.service';
...
@NgModule({
...
   providers: [GoogleAnalyticsService],
});
...
```

새로 생성된 서비스 파일 내에서 `gtag가` 호출될 때 본질적으로 실행하고 형식이 정확하고 `gtag`에서 요구하는 방식인지 확인하는 `public eventEmitter` 함수를 생성할 것입니다.

또한 외부에서 로드된 라이브러리에 서비스를 노출하려면 `gtag`를 Function으로 선언해야 합니다.

```
...
declare let gtag:Function;
...
export class GoogleAnalyticsService {
...
   public eventEmitter(
       eventName: string,
       eventCategory: string,
       eventAction: string,
       eventLabel: string = null,
       eventValue: number = null ){
            gtag('event', eventName, {
                    eventCategory: eventCategory,
                    eventLabel: eventLabel,
                    eventAction: eventAction,
                    eventValue: eventValue
                  })
       }
}
```

이 서비스를 사용하려면 컴포넌트로 가져와 버튼 클릭 이벤트와 같은 일이 발생할 때 트래킹 하려는 값을 `eventEmitter`에 전달하여 실행해야 합니다.

```
<button (click)="clickLoginEvent()">Login</button>
```

그런 다음 `clickLoginEvent()`를 처리할 구성 요소에서 `eventEmitter`를 호출할 수 있습니다. 작동하려면 생성한 서비스를 구성 요소로 가져와야 합니다.

```
...
import{GoogleAnalyticsService} from './google-analytics.service';
...
export class LoginComponent implements OnInit {
...
   clickLoginEvent(){
     this
     .googleAnalyticsService
     .eventEmitter("user_to_login", "main", "login", "click", 10);
   }
...
}
```

나만의 이벤트 및 이벤트 카테고리를 만들 수 있습니다. Google에는 사용할 수 있도록 미리 정의된 목록이 있습니다. 전체 목록은 여기에서 찾을 수 있습니다. (https://developers.google.com/analytics/devguides/collection/gtagjs/events)

## 마치며

단일 페이지 및 프로그레시브 웹 응용 프로그램에서 구현하는 것은 약간의 코드만 추가하면 되기 때문에 어려운 부분은 없습니다.
`gtag.js`와 Angular 라우팅의 작동 방식을 이해하고 Analytics 추적을 구현하는 것이 중요합니다.
`angular-google-tag-manager` 패키지를 설치하여 사용하는 방법도 있으니 참고 하면 좋을 것 같습니다.
(https://www.npmjs.com/package/angular-google-tag-manager)
