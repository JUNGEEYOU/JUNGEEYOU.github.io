---
layout: post
title: "8. 템플릿 메소드 패턴 적용(Template Method) with Python"
date: 2019-12-21
excerpt: ""
tags: [Template, Template method Pattern, 템플릿 패턴, 템플릿 패턴 예제, design pattern]
comments: true
design_patterns: true
---


# 템플릿 메소드 적용(wiht python)

# 1. 템플릿 메소드 패턴이란?

### 템플릿 메소드 패턴 정의

> 메소드에서 알고리즘의 골격을 정의합니다. 알고리즘의 여러 단계 중 일부는 서브 클래스에서 구현할 수 있습니다. 템플릿 메소드를 이용하면 알고리즘의 구조는 그대로 유지하면서 서브클래스에서 특정 단계를 재정의할 수 있습니다.

![wiht%20python/Untitled.png](/assets/img/design_pattern/template_ex/Untitled.png)

sourcemaking 이미지 참고

- 간단히 말하면 알고리즘의 틀을 만들기 위한 것
    - **틀(템플릿)이란?** 일련의 단계들로 알고리즘을 정의한 메소드

---

# 2. 템플릿 메소드 적용하기 🗞

> 여러 뉴스 사이트 최신 정보 수집기를 템플릿 메소드를 적용해서 만들어 보자.  순서는 아래와 같다.

1. url 얻고 피드 서버에 요청 보낸다
2. 가공되지 않은 콘텐츠 얻는다
3. 파싱한다.
4. 사용자에게 출력해서 보내준다.

### UML 다이어그램

![wiht%20python/Untitled%201.png](/assets/img/design_pattern/template_ex/Untitled%201.png)

## 코드

- 추상 클래스: 탬플릿 메소드 정의
{% highlight python %}
    import ssl
    from urllib.request import urlopen
    
    
    class AbstractNewsParser(object):
        def __init__(self):
            # 클래스 인스턴스 생성 방지
            if self.__class__ is AbstractNewsParser:
                raise TypeError("abstract class cannot be instatiated")
    
        def print_top_news(self):
            """
             템플릿 메소드. 모든 웹사이트에서 3개의 최신 뉴스를 반환한다.
            :return:
            """
            # 1. url 얻고 피드 서버에 요청 보낸다
            url = self.get_url()
            # 2. 가공되지 않은 콘텐츠 얻는다
            raw_content = self.get_raw_content(url)
            # 3. 파싱한다.
            content = self.parse_content(raw_content)
            # 4. 탑 3만 크롭
            cropped = self.crop(content)
            # 5. 사용자에게 출력해서 보내준다.
            for item in cropped:
                print("Title: ", item['title'])
                print("Content: ", item['content'])
                print("Link: ", item['link'])
                print("Published: ", item['published'])
                print("ID: ", item['id'])
    
        def get_url(self):
            """
            url 얻고 피드 서버에 요청 보낸다 - 추상 메소드
            :return:
            """
            raise NotImplementedError()
    
        def get_raw_content(self, url):
            """
            가공되지 않은 콘텐츠 얻는다
            :param url:
            :return:
            """
            context = ssl._create_unverified_context()
            return urlopen(url, context=context).read()
    
        def parse_content(self, content):
            """
            파싱한다 - 추상 메소드
            :param content:
            :return:
            """
            raise NotImplementedError()
    
        def crop(self, parsed_content, max_items=3):
            return parsed_content[:max_items]
{% endhighlight %}

- 구상 클래스 : GoogleParser, YahooParser
{% highlight python %}
    from xml.dom import minidom
    from news.AbstractNewsParser import AbstractNewsParser
    """
    <entry>
        <id>
        https://news.google.com/stories/CAAqOQgKIjNDQklTSURvSmMzUnZjbmt0TXpZd1NoTUtFUWpjbEpuRWxJQU1FY1otVlNZX3RhRmhLQUFQAQ?oc=5
        </id>
        <title type="html">유성엽 석패율 대상서 중진 제외… 청년·여성·정치신인 한정 - 매일경제 - 매일경제</title>
        <updated>2019-12-20T01:55:10.000000000Z</updated>
        <link href="https://news.google.com/__i/rss/rd/articles/CBMiOGh0dHBzOi8vd3d3Lm1rLmNvLmtyL25ld3MvcG9saXRpY3Mvdmlldy8yMDE5LzEyLzEwNjg1Mjcv0gE6aHR0cHM6Ly9tLm1rLmNvLmtyL25ld3MvcG9saXRpY3Mvdmlldy1hbXAvMjAxOS8xMi8xMDY4NTI3Lw?oc=5" type="text/html"/>
        <content type="html">
        <ol><li><a href="https://news.google.com/__i/rss/rd/articles/CBMiOGh0dHBzOi8vd3d3Lm1rLmNvLmtyL25ld3MvcG9saXRpY3Mvdmlldy8yMDE5LzEyLzEwNjg1Mjcv0gE6aHR0cHM6Ly9tLm1rLmNvLmtyL25ld3MvcG9saXRpY3Mvdmlldy1hbXAvMjAxOS8xMi8xMDY4NTI3Lw?oc=5" target="_blank">유성엽 석패율 대상서 중진 제외… 청년·여성·정치신인 한정 - 매일경제</a>&nbsp;&nbsp;<font color="#6f6f6f">매일경제</font></li><li><a href="https://news.google.com/__i/rss/rd/articles/CBMiK2h0dHBzOi8vd3d3LnlvdXR1YmUuY29tL3dhdGNoP3Y9aTJwYkM2R1FiOVnSAQA?oc=5" target="_blank">협상도 멈춰선 '4+1 선거법'…'석패율제' 장기 대치</a>&nbsp;&nbsp;<font color="#6f6f6f">JTBC News</font></li><li><strong><a href="https://news.google.com/stories/CAAqOQgKIjNDQklTSURvSmMzUnZjbmt0TXpZd1NoTUtFUWpjbEpuRWxJQU1FY1otVlNZX3RhRmhLQUFQAQ?oc=5" target="_blank">Google 뉴스에서 전체 콘텐츠 보기</a></strong></li></ol>
        </content>
    </entry>
    """
    class GoogleParser(AbstractNewsParser):
        """
            atom 피 받는 구상 클래스
        """
    
        def get_url(self):
            return "https://news.google.com/atom?hl=ko&gl=KR&ceid=KR:ko"
    
        def parse_content(self, raw_content):
            parsed_content = []
    
            dom = minidom.parseString(raw_content)
    
            for node in dom.getElementsByTagName("entry"):
                parsed_item = {}
                try:
                    parsed_item['title'] = node.getElementsByTagName('title')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['title'] = None
    
                try:
                    parsed_item['content'] = node.getElementsByTagName('content')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['content'] = None
    
                try:
                    parsed_item['link'] = node.getElementsByTagName('link')[0].getAttribute("href")
                except IndexError:
                    parsed_item['link'] = None
    
                try:
                    parsed_item['id'] = node.getElementsByTagName('id')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['id'] = None
    
                try:
                    parsed_item['published'] = node.getElementsByTagName('updated')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['published'] = None
                parsed_content.append(parsed_item)
            return parsed_content
    
    
    from xml.dom import minidom
    from news.AbstractNewsParser import AbstractNewsParser
    
    """
        <item>
            <title>
            In new Yahoo News/YouGov poll, most voters agree with Trump&#39;s impeachment — but support for his removal falls just short of 50%
            </title>
            <description>
            <p><a href="https://news.yahoo.com/in-new-yahoo-news-you-gov-poll-most-voters-agree-with-trumps-impeachment-but-support-for-his-removal-falls-just-short-of-50-002004515.html"><img src="http://l1.yimg.com/uu/api/res/1.2/yKUmQ6vZHHop1XVcNgIDIQ--/YXBwaWQ9eXRhY2h5b247aD04Njt3PTEzMDs-/https://media-mbst-pub-ue1.s3.amazonaws.com/creatr-uploaded-images/2019-12/63ec1200-22b2-11ea-86ff-1be5902577dc" width="130" height="86" alt="In new Yahoo News/YouGov poll, most voters agree with Trump&#39;s impeachment — but support for his removal falls just short of 50%" align="left" title="In new Yahoo News/YouGov poll, most voters agree with Trump&#39;s impeachment — but support for his removal falls just short of 50%" border="0" ></a>The bottom line is that registered voters favor the House’s decision to impeach the president by a 50 percent to 45 percent margin.<p><br clear="all">
            </description>
            <link>
            https://news.yahoo.com/in-new-yahoo-news-you-gov-poll-most-voters-agree-with-trumps-impeachment-but-support-for-his-removal-falls-just-short-of-50-002004515.html
            </link>
            <pubDate>Thu, 19 Dec 2019 19:20:04 -0500</pubDate>
            <source url="https://news.yahoo.com/">Yahoo News</source>
            <guid isPermaLink="false">
            in-new-yahoo-news-you-gov-poll-most-voters-agree-with-trumps-impeachment-but-support-for-his-removal-falls-just-short-of-50-002004515.html
            </guid>
            <media:content height="86" url="http://l1.yimg.com/uu/api/res/1.2/yKUmQ6vZHHop1XVcNgIDIQ--/YXBwaWQ9eXRhY2h5b247aD04Njt3PTEzMDs-/https://media-mbst-pub-ue1.s3.amazonaws.com/creatr-uploaded-images/2019-12/63ec1200-22b2-11ea-86ff-1be5902577dc" width="130"/>
            <media:text type="html">
            <p><a href="https://news.yahoo.com/in-new-yahoo-news-you-gov-poll-most-voters-agree-with-trumps-impeachment-but-support-for-his-removal-falls-just-short-of-50-002004515.html"><img src="http://l1.yimg.com/uu/api/res/1.2/yKUmQ6vZHHop1XVcNgIDIQ--/YXBwaWQ9eXRhY2h5b247aD04Njt3PTEzMDs-/https://media-mbst-pub-ue1.s3.amazonaws.com/creatr-uploaded-images/2019-12/63ec1200-22b2-11ea-86ff-1be5902577dc" width="130" height="86" alt="In new Yahoo News/YouGov poll, most voters agree with Trump&#39;s impeachment — but support for his removal falls just short of 50%" align="left" title="In new Yahoo News/YouGov poll, most voters agree with Trump&#39;s impeachment — but support for his removal falls just short of 50%" border="0" ></a>The bottom line is that registered voters favor the House’s decision to impeach the president by a 50 percent to 45 percent margin.<p><br clear="all">
            </media:text>
            <media:credit role="publishing company"/>
        </item>
    """
    
    class YahooParser(AbstractNewsParser):
        """
        야후 뉴스 받는 구상 클래스
        """
        def get_url(self):
            return "https://news.yahoo.com/rss"
    
        def parse_content(self, raw_content):
            parsed_content = []
    
            dom = minidom.parseString(raw_content)
    
            for node in dom.getElementsByTagName("item"):
                parsed_item ={}
                try:
                    parsed_item['title'] = node.getElementsByTagName('title')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['title'] = None
    
                try:
                    parsed_item['content'] = node.getElementsByTagName('description')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['content'] = None
    
                try:
                    parsed_item['link'] = node.getElementsByTagName('link')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['link'] = None
    
                try:
                    parsed_item['id'] = node.getElementsByTagName('guid')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['id'] = None
    
                try:
                    parsed_item['published'] = node.getElementsByTagName('pubDate')[0].childNodes[0].nodeValue
                except IndexError:
                    parsed_item['published'] = None
                parsed_content.append(parsed_item)
            return parsed_content
{% endhighlight %}
- test 코드
{% highlight python %}
    from news.GoogleParser import GoogleParser
    from news.YahooParser import YahooParser
    
    if __name__ == "__main__":
        google = GoogleParser()
        yahoo = YahooParser()
    
        print("Google:")
        google.print_top_news()
        print("Yahoo:")
        yahoo.print_top_news()
{% endhighlight %}
---

# 소스 코드

- [https://github.com/JUNGEEYOU/template_ex](https://github.com/JUNGEEYOU/template_ex)

# 참고

- python design patterns - python 예제 코드로 배우는 핵심 디자인 패턴
