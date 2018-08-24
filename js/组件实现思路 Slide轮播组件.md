## 接口定义
一般来说，一个基本的Slide组件可以让用户（其他程序员）初始化元素使其变成Slide。提供一系列函数：跳转到第几个slide，一下张，上一张等。以及一系列属性：是否自动播放，是否使用分页器等。


## 封装
### 依赖
JQuery

### 接口
- element: Slide组件必须使用ol,li元素。同时ol需要用一个div包裹，该div必须包含'.slides'
- autoPlay: 是否自动播放。true：自动播放； false：不自动播放
- pager: 是否使用分页器。true：使用。false：不使用。 **todo**

### 组件封装注意点
Slide组件的核心是go函数（跳转），它可以指定跳转到哪个slide。诸如：next（下一张），prev（上一张），play（开始播放），stop(停止播放)等函数都是直接或者间接通过它演变的。

### 代码

#### css

```
.easySlides {
            overflow: hidden;
            position: relative;
        }

        .easySlides ol {
            list-style: none;
            margin: 0;
            padding: 0;
            display: flex;
            transition: transform 0.5s;
        }

        .easySlides ol>li img {
            /* 去除图片和li之间的一点空隙 */
            vertical-align: top;
        }

        .easySlides-prev {
            position: absolute;
            top: 50%;
            left: 0;
            transform: translateY(-50%);
        }

        .easySlides-next {
            position: absolute;
            top: 50%;
            right: 0;
            transform: translateY(-50%);
        }

```

#### JS

```
 function Slide(options) {
                this.options = options;
                this.timer = undefined;
                this.$element = $(this.options.element);
                this.$element.addClass('easySlides');   
                this.initHtml();
                this.bindEvents();
                if (this.options.autoPlay) {
                    this.play.call(this);
                }       
                this.go(0);//默认显示第一张
            }
            
            Slide.prototype.initHtml = function(){
                var width = this.$element.children('ol').children('li').width();
                this.$element.width(width);
                this.$pre = $('<button class="easySlides-prev">上一页</button>');
                this.$next = $('<button class="easySlides-next">下一页</button>');
                this.$element.append(this.$pre);
                this.$element.append(this.$next);
            }

            Slide.prototype.bindEvents = function(){
                var slideObj = this;
                this.$pre.on('click', function () { slideObj.prev(); });
                this.$next.on('click', function () { slideObj.next(); });
                this.$element.on('mouseenter', function () {
                    slideObj.stop();
                }).on('mouseleave', function () {
                    slideObj.play();
                });
            }

            // go函数是核心
            Slide.prototype.go = function (index) {
                var $ol = this.$element.children('ol');
                var $items = $ol.children('li');
                if (index >= $items.length) {
                    index = 0;
                } else if (index < 0) {
                    index = $items.length - 1;
                }
                //移动当前图片的位置
                var offset = -index * this.$element.width();
                var moveStyle = 'translateX(' + offset + 'px)';
                $ol.css({ transform: moveStyle });
                this.current = index;
            }

            Slide.prototype.next = function () {
                this.go(this.current + 1);
            }

            Slide.prototype.prev = function () {
                this.go(this.current - 1);
            }

            Slide.prototype.play = function () {
                var slideObj = this;
                this.timer = setInterval(function () {
                    slideObj.go.call(slideObj, slideObj.current + 1);
                }, 2000);
            }

            Slide.prototype.stop = function () {
                window.clearInterval(this.timer);
            }

```

## 组件使用
#### HTML
```
 <div class="slides">
        <ol>
            <li>
                <img src="https://upload-images.jianshu.io/upload_images/9425951-af1f2219b760c6f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
            </li>
            <li>
                <img src="https://upload-images.jianshu.io/upload_images/9425951-8b4161b01c561abb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
            </li>
            <li>
                <img src="https://upload-images.jianshu.io/upload_images/9425951-d4c898c6be9611b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
            </li>

        </ol>
    </div>

```

#### JS
```
   var slide = new Slide({
        element: '.slides',
        autoPlay: true,
        controls: false,
        pager: false,
    });
```








