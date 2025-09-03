---
title: 演習の手順
permalink: index.html
layout: home
---

# GitHub Copilot の演習

以下のクイックスタート演習は、GitHub Copilot の機能を調べる実践的な学習エクスペリエンスを提供するように作られています。 各演習には、ラボ環境でやり終えることができる一連のタスクが含まれます。

## クイック スタート演習
<hr>

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}

{% for activity in labs %}

### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }})
{{ activity.lab.description }}
<hr>
{% endfor %}
