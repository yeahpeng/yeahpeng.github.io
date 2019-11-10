title: pyspider
date: 2019-07-28 22:39:42
categories: work
tags: 
- pyspider
- python
---

## Install
```
pip install pyspider
```

## Error

* 
```
ValueError: Invalid configuration:
  - Deprecated option 'domaincontroller': use 'http_authenticator.domain_controller' instead.
```
### [solution](https://blog.csdn.net/u011451186/article/details/88222328)
```
pip uninstall wsgidav
pip install wsgidav==2.4.1
```

* 页面部分无法加载
预览网页得时候，可能会出现空白页面，是因为pysipder不加载JavaScript代码，用fetch_type='js'，pyspider会自动调用phantomjs来渲染网页。
### solution
```
sudo apt-get install phantomjs
```

* 

```
(phantomjs:12253): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，

(phantomjs:12253): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，
phantomjs>
```

### solution
```
sudo apt-get install gtk2-engines-pixbuf
```

