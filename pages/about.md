---
layout: page
title: About
description: 小疯子日志
keywords: lingling hu, 玲玲
comments: true
menu: 关于
permalink: /about/
---

我是玲玲，码代码的小疯子。

仰慕「优雅编码的艺术」。

## 坚信

* 熟能生巧
* 努力改变人生

## 联系

* GitHub：[@hulinglingxin](https://github.com/hulinglingxin)
* 博客：[{{ site.title }}]({{ site.url }})
* 微博: [@小疯子_200208](http://weibo.com/u/5969988909)

## Skill Keywords

#### Software Engineer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_software_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Mobile Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_mobile_app_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Windows Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_windows_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>
