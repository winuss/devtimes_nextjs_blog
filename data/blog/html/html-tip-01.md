---
title: '개발자가 잘 모르는 HTML 태그'
date: '2022-01-16'
tags: ['web', 'html']
draft: false
summary: '웹을 개발시 아주 유용하면서 개발자들이 잘 모르는 태그들을 한번 살펴보도록 하겠습니다.'
---

웹을 개발하다보면 익숙한 HTML 태그들만 사용하게 되고 그외 기능적인 부분들은 javascript나 css로 구현을 하게 되는데 (div, span anchor, header, list.. 등) 아주 유용하면서 개발자들이 잘 모르는 태그들을 한번 살펴보도록 하겠습니다.
CSS ,JS로만 가능하다고 생각한 것을 이제 HTML로 심플하게 개발해 보세요.

## progress, meter

```
<progress value="50" min="0" max="100"></progress>
```

![progress](/posts/html/progress.mov.gif)

```
<meter value="10" min="0" max="100" low="20" high="65" optimum="15"></meter>
```

![meter](/posts/html/meter.png)

> `meter`는 속성에 따라 값이 높고 낮을때 색상을 다르게 설정하여 작업의 진행상태를 표현할 수 있다. 미세먼지 상태와 같은 좋음, 나쁨, 매우나쁨 등의 상태를 함께 표현하기에 적합합니다.

## details, summary

`mat-expansion-panel`과 같은 유저의 클릭으로 정보를 보여주고 숨기는 패턴 적용이 가능합니다.

```
<details>
	<summary>클릭 전 볼 수 있는 영역</summary>
	<span>클릭 후에만 표시되는 영역</span>
</details>
```

![details](/posts/html/details.mov.gif)

## 달력, 날짜 선택기

input 태그의 type으로 설정이 가능합니다. 단 브라우저마다 UI가 가르게 표현됩니다.

```
<input type="date"/>
<input type="week"/>
<input type="month"/>
<input type="time"/>
```

## picture

유저의 장치나 환경에 따라 각기 다른 버전의 이미지를 표시가허나 브라우저가 이미지 포맷을 지원하지 않을 때 다른 포맷을 제공할 수 있습니다.

```
<picture>
	<source srcset="src/01.jpeg" media="(min-width:1200px)"/>
    <source srcset="src/02.jpeg" media="(min-width:900px)"/>
    <source srcset="src/03.jpeg" media="(min-width:500px)"/>
    <img src="src/04.jpeg" />
</picture>
```

> 💡 환경에 맞는 이미지를 다운로드 해서 보여줄 수 있으므로, 페이지 로딩 속도를 높일 수 있음.

## datalist

javascript 없이 자동완성 기능을 만들수 있고 필터 기능까지 제공 합니다.

```
<label for="movie">What is your favourite movie?</label>
<input type="text" list="movie-options"/>

<datalist id="movie-options">
  <option value="Dune"/>
  <option value="Dark waters"/>
  <option value="The Artist"/>
  <option value="The Avengers"/>
  <option value="Iron Man"/>
  <option value="Iron Man II"/>
  <option value="Matrix"/>
</datalist>
```

> **주의사항**<br/>
> input의 list와 datalist의 id는 동일해야됨

![datalist](/posts/html/datalist.mov.gif)

## 마치며

유용하고 간단하게 사용할 수 있는 HTML태그 이니 꼭 기억해서 활요하면 좋을 것 같습니다.

(출처 : https://www.youtube.com/watch?v=EMOlLLTAZMs)
