---
layout: post
title: "리눅스 Mint 원격 하는법(Chrome 원격 데스크톱) "
date: 2019-12-02
excerpt: ""
tags: [Chrome 원격 데스크톱, 리눅스 민트, mint 원격 , mint chrome 원격, linux mint chrome remote desktop,  mint chrome remote desktop]
comments: true
etc: true
---
# 리눅스 Mint 원격 하는법(Chrome 원격 데스크톱)

> 원격을 편리하고 느림 없이 리눅스 민트에서도 가능한 "chrome  원격 데스크톱"을 설치해서 사용하자. 🤩🤩

## 1. chrome 에서 원격 데스크톱 추가한다.

- [https://chrome.google.com/webstore/detail/chrome-remote-desktop/inomeogfingihgjfjlpeplalcfajhgai?utm_source=chrome-ntp-icon](https://chrome.google.com/webstore/detail/chrome-remote-desktop/inomeogfingihgjfjlpeplalcfajhgai?utm_source=chrome-ntp-icon)

## 2. 호스트 구성요소에 적합한 Debian 패키지 설치

- [http://dl.google.com/linux/direct/chrome-remote-desktop_current_amd64.deb](http://dl.google.com/linux/direct/chrome-remote-desktop_current_amd64.deb)

## 3.  가상 데스크톱 세션 만들기

- 우분투와 다르게 민트는 아래와 같은 명령어를 사용해야한다.
{% highlight shell %}
    echo 'exec /etc/mdm/Xsession "cinnamon-session-cinnamon2d"' >> ~/.chrome-remote-desktop-session
    
    echo 'export CHROME_REMOTE_DESKTOP_DEFAULT_DESKTOP_SIZES="1366x768"' >> ~/.profile
{% endhighlight %}
## 4. 원격 연결 허용

1. 컴퓨터에서 Chrome을 엽니다.
2. 상단의 주소 표시줄에 `chrome://apps`를 입력한 후 **Enter**를 누릅니다.
3. Chrome 원격 데스크톱 을 클릭합니다.
4. '내 컴퓨터'에서 **시작하기**를 클릭합니다.
5. **원격 연결 사용**을 클릭합니다.
6. PIN을 입력한 다음 다시 한 번 입력하고 **확인**을 클릭합니다.
7. 확인 대화상자를 닫습니다.

---

## 참고 사이트

- [https://support.google.com/chrome/answer/1649523](https://support.google.com/chrome/answer/1649523)
- [https://towfiqueimam.wordpress.com/2017/05/19/chrome-remote-desktop-on-mint-18-1/](https://towfiqueimam.wordpress.com/2017/05/19/chrome-remote-desktop-on-mint-18-1/)
