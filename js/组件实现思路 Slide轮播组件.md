```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>

    <style>
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
    </style>

    <script>
        $(document).ready(function () {
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


            var slide = new Slide({
                element: '.slides',
                autoPlay: true,
                controls: false,
                pager: false,
            });



        })
    </script>

</head>

<body>

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


</body>

</html>
```
