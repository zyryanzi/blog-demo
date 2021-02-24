---
title: hexo-插入图片
date: 2021-02-23 09:58:49
tags:
    - hexo
    - hexo-asset-image
    - hexo 插入图片
categories: 
    - 计算机科学
    - hexo
---

*今天想要试试在hexo博客中插入图片，按照官方文档试了下没成功，*
*翻看了一下别人的经验~~，原来是这样。*
*记录于此*
<!--more-->

### 进入hexo博客根目录，安装个插件
    npm install hexo-asset-image --save

### 修改根目录下的 _config.yml
    post_asset_folder: true
这样就可以在 新建文章时在_post下面会生成一个和文章同名的文件夹，将要使用的图片等静态资源放在这里

### 修改 hexo-asset-image 中的源码，将 /node_modules/hexo-asset-image/index.js 中代码替换如下
    'use strict';
    var cheerio = require('cheerio');

    // http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
    function getPosition(str, m, i) {
    return str.split(m, i).join(m).length;
    }

    var version = String(hexo.version).split('.');
    hexo.extend.filter.register('after_post_render', function(data){
        var config = hexo.config;
        if(config.post_asset_folder){
    	    var link = data.permalink;
            if(version.length > 0 && Number(version[0]) == 3)
                var beginPos = getPosition(link, '/', 1) + 1;
            else
                var beginPos = getPosition(link, '/', 3) + 1;
	        // In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
	        var endPos = link.lastIndexOf('/') + 1;
            link = link.substring(beginPos, endPos);

            var toprocess = ['excerpt', 'more', 'content'];
            for(var i = 0; i < toprocess.length; i++){
                var key = toprocess[i];
 
                var $ = cheerio.load(data[key], {
                ignoreWhitespace: false,
                xmlMode: false,
                lowerCaseTags: false,
                decodeEntities: false
                });

                $('img').each(function(){
                    if ($(this).attr('src')){
                        // For windows style path, we replace '\' to '/'.
                        var src = $(this).attr('src').replace('\\', '/');
                        if(!/http[s]*.*|\/\/.*/.test(src) &&
                        !/^\s*\//.test(src)) {
                        // For "about" page, the first part of "src" can't be removed.
                        // In addition, to support multi-level local directory.
                        var linkArray = link.split('/').filter(function(elem){
                            return elem != '';
                        });
                        var srcArray = src.split('/').filter(function(elem){
                            return elem != '' && elem != '.';
                        });
                        if(srcArray.length > 1)
                            srcArray.shift();
                        src = srcArray.join('/');
                        $(this).attr('src', config.root + link + src);
                        console.info&&console.info("update link as:-->"+config.root + link + src);
                        }
                    }else{
                        console.info&&console.info("no src attr, skipped...");
                        console.info&&console.info($(this));
                    }
                });
                data[key] = $.html();
            }
        }
    });

如果不做以上替换，我生成的图片路径前面总是会加上 "/.top/" 前缀，导致图片访问不到

### 在文章中使用如下标签引用图片
    {% asset_img(这个不变) 图片名称.jpg 文字注释 %}
