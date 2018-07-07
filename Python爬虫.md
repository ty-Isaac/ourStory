---
title: Python爬虫--《山大主页》
date: 2017-04-23
categories: Python
tags: [python,爬虫,信息检索]
---

# Python爬虫

## URL管理器

声明两个集合 new_urls,old_urls. 一个存储待爬取的URL，一个存储已经爬取过的URLS。其中有四个函数，分别是

* add_new_url: 如果传入的url不在带爬取页面和已爬取页面中，向new_urls中加入一个新的待爬取页面。
* add_new_urls:传入很多urls，将urls的每一个url调用add_new_url判断是否加入待爬取集合中
* has_new_url:判断是否有待爬取页面
* get_new_url:从待爬取集合中返回一个url并将其加入已爬取集合中

```python
# coding:utf8
class UrlManager(object):
    def __init__(self):
        self.new_urls = set()
        self.old_urls = set()
    # 向管理器添加url
    def add_new_url(self, url):
        if url is None:
            return
        # 如果URL不在待爬取页面和已爬取页面中，将页面加入到待爬取页面
        if url not in self.new_urls and url not in self.old_urls:
            self.new_urls.add(url)
            
    def add_new_urls(self, urls):
        # 如果urls为空 或者 其长度为0 退出
        if urls is None or len(urls) == 0:
            return
        # 调用单个url加入到集合中
        for url in urls:
            self.add_new_url(url)

    # 是否有新的url
    def has_new_url(self):
        # 长度不为0表明有待爬取的url
        return len(self.new_urls) != 0

    def get_new_url(self):
        # 从待获取url集合中获取一个url，并加入到已爬取集合
        new_url = self.new_urls.pop()
        self.old_urls.add(new_url)
        return new_url
```



## URL下载器

将传入的url，使用urllib2.urlopen下载，并返回结果

```python
# coding:utf8
import urllib2


class HtmlDownloader(object):

    def download(self, url):
        if url is None:
            return None
        response = urllib2.urlopen(url)
        if response.getcode() != 200:
            return None
        return response.read()
```



## URL解析器

* _get_new_urls: 根据传入的BeautifulSoup对象查找符合要求的url，即需要爬取的url。找到所有a标签中符合url标准的 href值。并且根据格式生成对应的url加入到new_urls集合中。
* _get_new_data: 解析页面中的数据，声明一个字典res_data，保存url、title、date、content、author
* parse: 传入页面内容和url，分别爬取页面的url和data并返回

```python
# coding:utf8


import re
import urlparse
from bs4 import BeautifulSoup


class HtmlParser(object):

    def _get_new_urls(self, page_url, soup):
        new_urls = set()
        # view.sdu.edu.cn/new/2017~~~~
        #^\d+/([^# ]*)| 导航栏： ^/new/\w+/$  上下一页^\w+/\d+.html ：sdnews/23.html  ^/new/\w+/$
        links = soup.find_all('a', href=re.compile(r"^\d+/([^# ]*)|^/new/\w+|^\w+/\d+.html"))    # 正则表达式  /view/123.htm
        for link in links:
            new_url = link['href']
            # print (new_url)
            if new_url[0:4] == "/new":
                new_full_url = urlparse.urljoin(page_url, new_url)
            else:
                new_full_url = "http://www.view.sdu.edu.cn/new/"+new_url
            #
            # print (new_full_url)
            new_urls.add(new_full_url)
        return new_urls

    def _get_new_data(self, page_url, soup):
        # 解析数据
        res_data = {}
        res_data['url'] = page_url
        # <div class="text"> <h1>山东大学召开创新转化学院课程与教材建设研讨会</h1>
        if soup.find('div', class_="text") is None:
            return
        # 标题
        title_node = soup.find('div', class_="text").find('h1')
        res_data['title'] = title_node.get_text()
        # 时间
        #<div class="text_a">
        date_node = soup.find('div', class_="text_a")
        res_data['date'] = date_node.get_text()

        # <div class="text_s" id="content">
        # 内容
        summary_node = soup.find('div', class_="text_s", id="content")
        res_data['summary'] = summary_node.get_text()
        # <div class ="text_c" > 【作者：邹晓东　来自：《文史】 < / div >
        # 作者
        author_node = soup.find('div',class_="text_c")
        res_data['author'] = author_node.get_text()
        return res_data

    def parse(self, page_url, html_cont):

        if page_url is None or html_cont is None:
            return
        soup = BeautifulSoup(html_cont, 'html.parser', from_encoding='utf-8')
        # soup = BeautifulSoup(html_cont)
        new_urls = self._get_new_urls(page_url, soup)
        new_data = self._get_new_data(page_url, soup)
        return new_urls, new_data

```



## DATA输出器

集合datas保存所有内容

* collect_data: 保存所有爬取的新闻内容数据
* output_html: 将datas中所有保存的数据写入到文件中

```python
# coding:utf8

import os

class HtmlOutputer(object):
    def __init__(self):
        self.datas = []

    def collect_data(self, data):
        if data is None:
            return
        self.datas.append(data)

    def output_html(self):
        foutAll = open('output.html', 'w')
        foutAll.write("<meta charset=\"UTF-8\">")
        foutAll.write("<html>")
        foutAll.write("<body>")
        foutAll.write("<table>")
        count = 0
        for data in self.datas:
            #记录每一篇文章的html
            foutAll.write("<tr>")
            foutAll.write("<td><a href=%s>%s</a></td>" % (data['url'], data['url']))
            foutAll.write("<td>%d</td>" % count)
            foutAll.write("<td>%s</td>" % data['title'].encode('utf-8'))

            fout=open('test/%d' % count, 'w')
            fout.write(data['title'].encode('utf-8'))
            fout.write(data['summary'].encode('utf-8'))
            fout.write(data['url'])
            fout.close()
            count += 1
```

## 爬虫启动器

* 初始化爬虫对象：URL管理器、URL下载器、URL解析器、URL输出器
* 爬虫调度程序：将root_url加入到URL管理器的待爬集合中。如果待爬集合不为空循环执行：取一个url，将其内容下载；将url，cont传入到解析器；解析器返回页面中所有的url和data。将url加入待爬、将data加入输出器。循环结束，调用输出

```python
# coding:utf8
from Spider import html_downloader
from Spider import html_output
from Spider import html_parser
from Spider import url_manager


class SpiderMain(object):
    # 构造函数初始化对象
    def __init__(self):
        self.urls = url_manager.UrlManager()                    # URL管理器
        self.downloader = html_downloader.HtmlDownloader()      # URL下载器
        self.parser = html_parser.HtmlParser()                  # URL解析器
        self.output = html_output.HtmlOutputer()                # 输出器
    # 爬虫调度程序

    def craw(self, root_url):
        count = 1
        self.urls.add_new_url(root_url)
        while self.urls.has_new_url():
            try:
                new_url = self.urls.get_new_url()
                # print 'craw %d : %s' % (count, new_url)
                html_cont = self.downloader.download(new_url)
                new_urls, new_data = self.parser.parse(new_url, html_cont)
                self.urls.add_new_urls(new_urls)
                self.output.collect_data(new_data)
                count += 1
            except:
                print 'craw failed'
                print count
        self.output.output_html()


if __name__ == "__main__":
    # root_url = "http://www.view.sdu.edu.cn/"             # 入口地址
    # root_url = "http://www.view.sdu.edu.cn/new/2015/1028/76105.html"             # 入口地址
    root_url = "http://www.view.sdu.edu.cn/"
    # root_url = "http://www.view.sdu.edu.cn/new/sdnews/"
    obj_spider = SpiderMain()
    obj_spider.craw(root_url)
```

[源代码请参考](http://download.csdn.net/detail/li201400130091/9859107)

