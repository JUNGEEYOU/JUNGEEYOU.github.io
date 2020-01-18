---
layout: post
title: "7-2. 퍼사드 패턴"
date: 2019-12-17
excerpt: ""
tags: [Facade, Facade Pattern, 퍼사드 패턴, head first design patterns, design pattern]
comments: true
design_patterns: true
---
# 👀 학습 목표

- 퍼사드 패턴을 이해하자. 

---

# 1. 퍼사드 패턴이란?

- 퍼사이드 패턴은 단순화된 인터페이스를 통해서 서브시스템을 더 쉽게 사용하기 위한 용도로 사용됩니다.
- 예를 들면, 컴퓨터를 켜면 운영 체제는 모든 컴퓨터의 내부 동작은 숨기고 간단한 인터페이만 사용자에게 제공한다. 또 다른 예로는 자동차가 있다.
{% highlight text %}
    어떤 서브시스템의 일련의 인터페이스에 대한 통합된 인터페이스를 제공합니다. 
    퍼사드에서 고수준 인터페이스를 정의하기 때문에 서브시스템을 더 쉽게 사용할 수 있습니다. 
{% endhighlight %}
## 퍼사드 패턴 다이어그램

![Python/Untitled.png](/assets/img/design_pattern/facade/Untitled.png)

# 2. python에서 사용되는 퍼사드 패턴

- 아래와 같은 json 모듈도 퍼사드 패턴 중 하나로 실제 loads() 메소드 자세한 부분은 숨기고 있다. .
{% highlight python %}
    import json 
    conetent = json.loads(res._content)
{% endhighlight %}
- load() 메소드 구현
{% highlight python %}
    def loads(s, encoding=None, cls=None, object_hook=None, parse_float=None,
            parse_int=None, parse_constant=None, object_pairs_hook=None, **kw):
        if (cls is None and encoding is None and object_hook is None and
                parse_int is None and parse_float is None and
                parse_constant is None and object_pairs_hook is None and not kw):
            return _default_decoder.decode(s)
        if cls is None:
            cls = JSONDecoder
        if object_hook is not None:
            kw['object_hook'] = object_hook
        if object_pairs_hook is not None:
            kw['object_pairs_hook'] = object_pairs_hook
        if parse_float is not None:
            kw['parse_float'] = parse_float
        if parse_int is not None:
            kw['parse_int'] = parse_int
        if parse_constant is not None:
            kw['parse_constant'] = parse_constant
        return cls(encoding=encoding, **kw).decode(s)
{% endhighlight %}
---
# 참고

- Head first design patterns 책



