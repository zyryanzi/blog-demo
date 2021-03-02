---
title: hexo-next-ribbon
date: 2021-03-02 10:59:14
tags: 
    - hexo
    - theme-next
    - ribbon
categories:
    - 计算机技术
    - hexo
---

*在浏览别人的博客时发现背景上的彩带很漂亮，请教搜索引擎，发现是一个叫 ribbon 的js插件*
*没想到一个小小js，用起来也是有不少坑的，开启了我这次的爬坑之旅*
<!--more-->
### 错误的尝试
最初的想法，随便百度一篇，5分钟解决。结果折腾了半个小时，看了好多文章，都要付费下载js。
这对白嫖党简直就是人格的侮辱（虽然选择白嫖，基本已经放弃了人格）。伤害性不强，侮辱性极强。
随后之后默默打开github，找寻源码。虽然毫不费力的找到了作者的源码，但是由于帝都召开某种宇宙神秘大会，导致网络很不稳定，又是半个小时，还是没有克隆下来。

### 强大的 theme-next 
到窗口吹了一阵凛冽的春风，灵机一动，我用的next主题，对很多第三方都有兼容，到配置文件中一搜索，果然有戏。
按照官方文档一通操作。
    https://theme-next.js.org/docs/third-party-services/external-libraries.html?highlight=ribbon

啊哈~~~ 彩带出来了！咦？不对，别人的是会动的，为什么我的傻傻的呆在那，鼠标点一下，才动一下，而且是瞬移。
继续爬坑吧！！！

### 一个神奇的网页
    https://blog.csdn.net/qq_29834241/article/details/108578756

这位大大给出了很好的解决方案，防止以后找不到页面，于此记录，并将内容存下

#### 放开ribbon配置
    canvas_ribbon:
        enable: true
        size: 90 # The width of the ribbon
        alpha: 0.6 # The transparency of the ribbon
        zIndex: -1 # The display level of the ribbon

#### 新建js文件
新建 theme/next/source/js/ribbon/ribbon.js 并将如下代码考入 (好长一大段)

    /**
    * Ribbons Class File.
    * Creates low-poly ribbons background effect inside a target container.
    */
    (function (name, factory)
    {
        if (typeof window === "object")
        {
            window[name] = factory();
        }

    })("Ribbons", function ()
    {
        var _w = window,
        _b = document.body,//返回html dom中的body节点 即<body>
        _d = document.documentElement;//返回html dom中的root 节点 即<html>

        // random helper
        var random = function ()
        {
            if (arguments.length === 1) // only 1 argument
                {
                    if (Array.isArray(arguments[0])) // extract index from array
                        {
                            var index = Math.round(random(0, arguments[0].length - 1));
                            return arguments[0][index];
                        }
                    return random(0, arguments[0]); // assume numeric
                } else
            if (arguments.length === 2) // two arguments range
                {
                    return Math.random() * (arguments[1] - arguments[0]) + arguments[0];
                }
            return 0; // default
        };

        // screen helper
        var screenInfo = function (e)
        {
            var width = Math.max(0, _w.innerWidth || _d.clientWidth || _b.clientWidth || 0),
            height = Math.max(0, _w.innerHeight || _d.clientHeight || _b.clientHeight || 0),
            scrollx = Math.max(0, _w.pageXOffset || _d.scrollLeft || _b.scrollLeft || 0) - (_d.clientLeft || 0),
            scrolly = Math.max(0, _w.pageYOffset || _d.scrollTop || _b.scrollTop || 0) - (_d.clientTop || 0);

            return {
                width: width,
                height: height,
                ratio: width / height,
                centerx: width / 2,
                centery: height / 2,
                scrollx: scrollx,
                scrolly: scrolly };

        };

        // mouse/input helper
        var mouseInfo = function (e)
        {
            var screen = screenInfo(e),
            mousex = e ? Math.max(0, e.pageX || e.clientX || 0) : 0,
            mousey = e ? Math.max(0, e.pageY || e.clientY || 0) : 0;

            return {
                mousex: mousex,
                mousey: mousey,
                centerx: mousex - screen.width / 2,
                centery: mousey - screen.height / 2 };

        };

        // point object
        var Point = function (x, y)
        {
            this.x = 0;
            this.y = 0;
            this.set(x, y);
        };
        Point.prototype = {
            constructor: Point,

            set: function (x, y)
            {
                this.x = x || 0;
                this.y = y || 0;
            },
            copy: function (point)
            {
                this.x = point.x || 0;
                this.y = point.y || 0;
                return this;
            },
            multiply: function (x, y)
            {
                this.x *= x || 1;
                this.y *= y || 1;
                return this;
            },
            divide: function (x, y)
            {
                this.x /= x || 1;
                this.y /= y || 1;
                return this;
            },
            add: function (x, y)
            {
                this.x += x || 0;
                this.y += y || 0;
                return this;
            },
            subtract: function (x, y)
            {
                this.x -= x || 0;
                this.y -= y || 0;
                return this;
            },
            clampX: function (min, max)
            {
                this.x = Math.max(min, Math.min(this.x, max));
                return this;
            },
            clampY: function (min, max)
            {
                this.y = Math.max(min, Math.min(this.y, max));
                return this;
            },
            flipX: function ()
            {
                this.x *= -1;
                return this;
            },
            flipY: function ()
            {
                this.y *= -1;
                return this;
            } };


        // class constructor
        var Factory = function (options)
        {
            this._canvas = null;
            this._context = null;
            this._sto = null;
            this._width = 0;
            this._height = 0;
            this._scroll = 0;
            this._ribbons = [];
            this._options = {
                // ribbon color HSL saturation amount
                colorSaturation: "80%",
                // ribbon color HSL brightness amount
                colorBrightness: "60%",
                // ribbon color opacity amount
                colorAlpha: 0.65,
                // how fast to cycle through colors in the HSL color space
                colorCycleSpeed: 6,
                // where to start from on the Y axis on each side (top|min, middle|center, bottom|max, random)
                verticalPosition: "center",
                // how fast to get to the other side of the screen
                horizontalSpeed: 200,
                // how many ribbons to keep on screen at any given time
                ribbonCount: 3,
                // add stroke along with ribbon fill colors
                strokeSize: 0,
                // move ribbons vertically by a factor on page scroll
                parallaxAmount: -0.5,
                // add animation effect to each ribbon section over time
                animateSections: true };

            this._onDraw = this._onDraw.bind(this);
            this._onResize = this._onResize.bind(this);
            this._onScroll = this._onScroll.bind(this);
            this.setOptions(options);
            this.init();
        };

        // class prototype
        Factory.prototype = {
            constructor: Factory,

            // Set and merge local options
            setOptions: function (options)
            {
                if (typeof options === "object")
                {
                    for (var key in options)
                    {
                        if (options.hasOwnProperty(key))
                        {
                            this._options[key] = options[key];
                        }
                    }
                }
            },

            // Initialize the ribbons effect
            init: function ()
            {
                try
                {
                    this._canvas = document.createElement("canvas");
                    this._canvas.style["display"] = "block";
                    this._canvas.style["position"] = "fixed";
                    this._canvas.style["margin"] = "0";
                    this._canvas.style["padding"] = "0";
                    this._canvas.style["border"] = "0";
                    this._canvas.style["outline"] = "0";
                    this._canvas.style["left"] = "0";
                    this._canvas.style["top"] = "0";
                    this._canvas.style["width"] = "100%";
                    this._canvas.style["height"] = "100%";
                    this._canvas.style["z-index"] = "-1";
                    this._canvas.style["background-color"]="#1f1f1f";
                    this._canvas.id = "bgCanvas";	
                    this._onResize();

                    this._context = this._canvas.getContext("2d");
                    this._context.clearRect(0, 0, this._width, this._height);
                    this._context.globalAlpha = this._options.colorAlpha;

                    window.addEventListener("resize", this._onResize);
                    window.addEventListener("scroll", this._onScroll);
                    document.body.appendChild(this._canvas);
                }
                catch (e) {
                    console.warn("Canvas Context Error: " + e.toString());
                    return;
                }
                this._onDraw();
            },

            // Create a new random ribbon and to the list
            addRibbon: function ()
            {
                // movement data
                var dir = Math.round(random(1, 9)) > 5 ? "right" : "left",
                stop = 1000,
                hide = 200,
                min = 0 - hide,
                max = this._width + hide,
                movex = 0,
                movey = 0,
                startx = dir === "right" ? min : max,
                starty = Math.round(random(0, this._height));

                // asjust starty based on options
                if (/^(top|min)$/i.test(this._options.verticalPosition))
                {
                    starty = 0 + hide;
                } else
                if (/^(middle|center)$/i.test(this._options.verticalPosition))
                {
                    starty = this._height / 2;
                } else
                if (/^(bottom|max)$/i.test(this._options.verticalPosition))
                {
                    starty = this._height - hide;
                }

                // ribbon sections data
                var ribbon = [],
                point1 = new Point(startx, starty),
                point2 = new Point(startx, starty),
                point3 = null,
                color = Math.round(random(0, 360)),
                delay = 0;

                // buils ribbon sections
                while (true)
                {
                    if (stop <= 0) break;stop--;

                    movex = Math.round((Math.random() * 1 - 0.2) * this._options.horizontalSpeed);
                    movey = Math.round((Math.random() * 1 - 0.5) * (this._height * 0.25));

                    point3 = new Point();
                    point3.copy(point2);

                    if (dir === "right")
                    {
                        point3.add(movex, movey);
                        if (point2.x >= max) break;
                    } else
                    if (dir === "left")
                    {
                        point3.subtract(movex, movey);
                        if (point2.x <= min) break;
                    }
                    // point3.clampY( 0, this._height );

                    ribbon.push({ // single ribbon section
                        point1: new Point(point1.x, point1.y),
                        point2: new Point(point2.x, point2.y),
                        point3: point3,
                        color: color,
                        delay: delay,
                        dir: dir,
                        alpha: 0,
                        phase: 0 });


                    point1.copy(point2);
                    point2.copy(point3);

                    delay += 4;
                    color += this._options.colorCycleSpeed;
                }
                this._ribbons.push(ribbon);
            },

            // Draw single section
            _drawRibbonSection: function (section)
            {
                if (section)
                {
                    if (section.phase >= 1 && section.alpha <= 0)
                    {
                        return true; // done
                    }
                    if (section.delay <= 0)
                    {
                        section.phase += 0.02;
                        section.alpha = Math.sin(section.phase) * 1;
                        section.alpha = section.alpha <= 0 ? 0 : section.alpha;
                        section.alpha = section.alpha >= 1 ? 1 : section.alpha;

                        if (this._options.animateSections)
                        {
                            var mod = Math.sin(1 + section.phase * Math.PI / 2) * 0.1;

                            if (section.dir === "right")
                            {
                                section.point1.add(mod, 0);
                                section.point2.add(mod, 0);
                                section.point3.add(mod, 0);
                            } else {
                                section.point1.subtract(mod, 0);
                                section.point2.subtract(mod, 0);
                                section.point3.subtract(mod, 0);
                            }
                            section.point1.add(0, mod);
                            section.point2.add(0, mod);
                            section.point3.add(0, mod);
                        }
                    } else
                    {section.delay -= 0.5;}

                    var s = this._options.colorSaturation,
                    l = this._options.colorBrightness,
                    c = "hsla(" + section.color + ", " + s + ", " + l + ", " + section.alpha + " )";

                    this._context.save();

                    if (this._options.parallaxAmount !== 0)
                    {
                        this._context.translate(0, this._scroll * this._options.parallaxAmount);
                    }
                    this._context.beginPath();
                    this._context.moveTo(section.point1.x, section.point1.y);
                    this._context.lineTo(section.point2.x, section.point2.y);
                    this._context.lineTo(section.point3.x, section.point3.y);
                    this._context.fillStyle = c;
                    this._context.fill();

                    if (this._options.strokeSize > 0)
                    {
                        this._context.lineWidth = this._options.strokeSize;
                        this._context.strokeStyle = c;
                        this._context.lineCap = "round";
                        this._context.stroke();
                    }
                    this._context.restore();
                }
                return false; // not done yet
            },

            // Draw ribbons
            _onDraw: function ()
            {
                // cleanup on ribbons list to rtemoved finished ribbons
                for (var i = 0, t = this._ribbons.length; i < t; ++i)
                {
                    if (!this._ribbons[i])
                    {
                        this._ribbons.splice(i, 1);
                    }
                }

                // draw new ribbons 
                this._context.clearRect(0, 0, this._width, this._height);

                for (var a = 0; a < this._ribbons.length; ++a) // single ribbon
                {
                    var ribbon = this._ribbons[a],
                    numSections = ribbon.length,
                    numDone = 0;

                    for (var b = 0; b < numSections; ++b) // ribbon section
                    {
                        if (this._drawRibbonSection(ribbon[b]))
                        {
                            numDone++; // section done
                        }
                    }
                    if (numDone >= numSections) // ribbon done
                        {
                            this._ribbons[a] = null;
                        }
                }
                // maintain optional number of ribbons on canvas
                if (this._ribbons.length < this._options.ribbonCount)
                {
                    this.addRibbon();
                }
                requestAnimationFrame(this._onDraw);
            },

            // Update container size info
            _onResize: function (e)
            {
                var screen = screenInfo(e);
                this._width = screen.width;
                this._height = screen.height;

                if (this._canvas)
                {
                    this._canvas.width = this._width;
                    this._canvas.height = this._height;

                    if (this._context)
                    {
                        this._context.globalAlpha = this._options.colorAlpha;
                    }
                }
            },

            // Update container size info
            _onScroll: function (e)
            {
                var screen = screenInfo(e);
                this._scroll = screen.scrolly;
            } };
        // export
        return Factory;
    });
    new Ribbons();

#### 配置 vendors
    vendors:
        canvas_ribbon: /js/ribbon/ribbon.js

### 减少主题侵入，渲染时忽略自定义js
虽然已经可以正常显示漂亮了的彩带了。但是js写在主题内，总觉得不太舒服，于是将其搬到了根目录下source/js内。
这样在编译时，就可以将其打入public/js了。
但是在 hexo g 的时候，总是会对js文件进行渲染，结果编译好的js文件中代码就变了。这可如何是好？
请教搜索引擎之后发现了这个参数：**skip_render**，位于根目录下的 _config.yml。可以是你忽略指定位置或类型的文件

    skip_render: "test.html"    # source 文件夹下指定文件

    skip_render: test/*         # 单个文件夹下全部文件

    skip_render: test/*.md      # 单个文件夹下指定类型文件

    skip_render: test/**        # 单个文件夹下全部文件及子文件

    skip_render:                # 多个文件夹以及其他情况
      - test/*
      - test/*.html

这里要注意 ‘*’ 和 “**” 的区别。

**至此，全部搞完，可以让彩带飘啊飘了！**