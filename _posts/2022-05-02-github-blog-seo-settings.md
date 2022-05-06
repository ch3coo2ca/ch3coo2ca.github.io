---
layout: post
date: 2022-05-02 12:01:00
title: "Github 블로그 SEO 설정하기 (feat. 구글 검색엔진)"
description: "Github 블로그가 구글 검색엔진에 검색되도록 설정해보자"
lastmod: 2022-05-06 19:29:00
tags: [SEO, Github Blog]
---

블로그를 만들었는데 구글에 아무리 검색해도 나오지 않는다 🤔.  코드를 확인해보니 현재 사용하고 있는 테마는 `jekyll-seo-tag` 플러그인을 이용해서 기본 meta 태그들은 생성이 되어있다. 구글 검색엔진에 블로그 글이 뜰 수 있도록 설정하는 방법을 알아보자. 

![구글에 검색해본 화면 (내 블로그 어디갔어..)](https://user-images.githubusercontent.com/38097442/166134241-60016bc1-2547-4f12-bc8d-0191d90318ba.png)
_구글에 검색해본 화면 (내 블로그 어디갔어..)_

<br>

## sitemap.xml 생성하기

sitemap.xml은 **웹 사이트 내 존재하는 모든 페이지의 목록을 나열한 파일**로 책의 **목차와 같은 역할**을 한다. 사이트맵을 제출하면 Google과 같은 검색엔진이 이 파일을 읽고 사이트를 더 효율적으로 크롤링한다. 

1. 프로젝트 루트 디렉토리에 `sitemap.xml` 파일을 생성한다. 
2. sitemap.xml 파일에 아래 코드를 붙여넣는다. 이 때 맨위의 `---` 두 줄을 꼭 포함하여야 한다. 
    
    ```{%raw%}
    ---
    ---
    <?xml version="1.0" encoding="UTF-8"?>
    <urlset 
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd" 
        xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
      {% for post in site.posts %}
        <url>
          <loc>{{ site.url }}{{ post.url }}</loc>
          {% if post.lastmod == null %}
            <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
          {% else %}
            <lastmod>{{ post.lastmod | date_to_xmlschema }}</lastmod>
          {% endif %}
    
          {% if post.sitemap.changefreq == null %}
            <changefreq>daily</changefreq>
          {% else %}
            <changefreq>{{ post.sitemap.changefreq }}</changefreq>
          {% endif %}
    
          {% if post.sitemap.priority == null %}
              <priority>0.5</priority>
          {% else %}
            <priority>{{ post.sitemap.priority }}</priority>
          {% endif %}
    
        </url>
      {% endfor %}
    </urlset>
    {%endraw%}
    ```
    
3. `https://블로그 URL/sitemap.xml` 로 접속하여 파일 내용이 정상적으로 출력되는지 확인한다.
![sitemap.xml](https://user-images.githubusercontent.com/38097442/166186114-cca9af2d-d3ae-46f6-9a9d-c73408126a68.png)
_https://ch3coo2ca.github.io/sitemap.xml_

<br>

## 포스트 내에서 sitemap 설정하기

루트에 설정한 sitemap.xml은 모든 포스트에 기본으로 적용되는 값이 지정되어있다. 
포스트 내에 사이트맵 관련 설정을 추가할 수도 있는데 **스크랩 주기, 스크랩 우선순위, 마지막 수정일** 등에 대한 정보를 넣을 수 있다. 

```yaml
---
layout: post
title: "TypeScript 유틸리티 타입"
tags: TypeScript
lastmode: 2022-05-01 16:00:00
sitemap: 
    changefreq : daily
    priority : 0.8
---
```

- `lastmode` : 마지막 수정일
- `sitemap.changefreq` : 스크랩 주기
    - always, hourly, daily, weekly, monthly, yearly, never 값을 줄 수 있다.
- `sitemap.priority` : 스크랩 우선순위
    - 특정 URL의 상대적 우선순위로 유효 값 범위는 0.0 ~ 1.0 이다.
    - 페이지 기본 우선순위는 0.5이다.

<br>

## robots.txt 생성하기

robots.txt 파일은 크롤러가 액세스할 수 있는 URL을 검색엔진 크롤러에 알려준다. 

1. 프로젝트 루트 디렉토리에 `robots.txt` 파일을 생성한다
2. robots.txt 파일에 아래 코드를 붙여넣는다. 이 때 `Sitemap` 의 주소는 자신의 블로그 URL로 수정하면 된다. 

```
User-agent: *
Allow: /
Sitemap: https://ch3coo2ca.github.io/sitemap.xml
```


`sitemap.xml` 과 `robots.txt` 파일 설정이 끝났다면, 커밋하고 원격 레포지토리에 푸쉬한다.
이제 구글 검색엔진에 등록하는 방법을 알아보자.

<br>

## 구글 검색엔진에 등록하기

#### 1. [Google Search Console](https://search.google.com/search-console/welcome?hl=ko&utm_source=wmx&utm_medium=deprecation-pane&utm_content=home) 접속

구글 웹마스터 도구에 접속하면 아래와 같은 화면이 나타난다. **`URL 접두어 → 등록하려는 사이트 URL 입력 → 계속`** 버튼 클릭한다. 

![google search console main](https://user-images.githubusercontent.com/38097442/166135127-991781ac-a2f7-44e8-a8e6-fbd2b7cae622.png)
_Google Search Console 화면_
    
<br>
URL 등록이 완료되면 아래와 같은 팝업이 뜬다. 여기서 **`속성으로 이동`**을 클릭한다.

![comple popup](https://user-images.githubusercontent.com/38097442/166135067-596aaba3-a4af-40f4-a493-ca44be81bf0d.png)
_URL 등록 완료 화면_

<br>

#### 2. sitemap 등록하기

왼쪽 상단의 사이드 메뉴를 열어서 **`색인 > Sitemaps` 메뉴 클릭 → 새 사이트맵 추가에 `https://블로그 URL/sitemap.xml` 입력 → 제출** 버튼을 클릭한다. 

![sitemap](https://user-images.githubusercontent.com/38097442/166135071-e06df541-c3bc-423d-8419-d0f9eac278f0.png)

<br>
제출이 완료되면 아래 화면에서 상태가 `성공` 이라고 나와야 되는데 오류가 나타난다. 처음에 sitemap.xml 오타낸채로 푸쉬했는데 갱신이 안되는 것 같다. 며칠 뒤에 데이터가 쌓이면 다시 확인해봐야겠다.

![sitemap error](https://user-images.githubusercontent.com/38097442/166136144-ab08d7c9-184e-4777-a7d9-a637e6563b3d.png)

<br>

![그렇게 사이트맵 제출하고 3일 넘게 지났다. . ](https://user-images.githubusercontent.com/38097442/167114166-7566e97d-0f5f-4854-ab58-976d3b294bf4.png)
_그렇게 사이트맵 제출하고 3일 넘게 지났다. . ._

4일이 지나도 갱신된 사이트맵을 크롤링해가지 않은 것 같다. ㅠㅠ 사이드메뉴에서 URL 검사를 클릭하고 블로그 주소를 입력해보았는데 아직 구글에 등록이 되어있지 않다고 나온다.  아래 화면에서 `색인 생성 요청` 버튼을 누르고 다시 기다려봐야겠다. 수동으로 색인 요청을 하면 **URL이 우선순위 크롤링 대기열에 추가**된다고 한다. 

![Google Search Console > 사이드 메뉴 > URL 검사 > 블로그 주소 입력](https://user-images.githubusercontent.com/38097442/167114181-2708935e-a7d0-45a6-a381-c7e615378a6c.png)
_Google Search Console > 사이드 메뉴 > URL 검사 > 블로그 주소 입력_

<br>
위 페이지에서 색인 등록 요청을 하고 약 16시간 지난 시점에 구글에 블로그 검색을 해봤다. 드디어 성공적으로 검색이 된다! 🎉🎉🎉

![search blog url in google](https://user-images.githubusercontent.com/38097442/167114200-fef0b704-3af4-4378-b6c7-52515a5068f7.png)
_구글에 다시 블로그 검색해 본 화면_

수동으로 URL 등록 요청을 하면 각각의 페이지에 대해서 등록해줘야 하는 번거로움이 있다. 우선 메인 블로그는 검색이 잘 되니.. 구글이 다시 사이트맵 크롤링해갈 때까지 기다려볼 예정이다.

<br>

## 참고
- [robots.txt와 sitemap.xml 알아보기](https://www.twinword.co.kr/blog/basic-technical-seo/)
- [Github 블로그 검색엔진에 등록하기](https://yenarue.github.io/tip/2020/04/30/Search-SEO/)