
## 设计接口
可以设想用户会这样使用Suggestion组件

- input: 需要初始化的元素
- search(text,callback)
  - text: 输入的数据
  - callback: 回调函数。获取数据一般都是需要从server端拿的，获取完成后用这个callback来绘制组件的下拉框
- emptyTemplate： 返回值为空时显示内容
- loadingTempate：数据在加载时显示的内容
```
var suggestion = new Suggestion({
   input: '#x',
   search: function(text,callback){
      callback(['a','aa','bb']);
   },
   emptyTemplate: '没有结果',
   loadingTempate: '<b>正在加载中.....</bar>'

})
```


```


<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://code.jquery.com/jquery-3.1.0.js"></script>

    <style>
        .easySuggestion {
            position: relative;
            border: 1px solid red;
            display: inline-block;
        }

        .easySuggestion-loading {
            position: absolute;
            top: 100%;
            left: 0;
            display: none;
        }

        .easySuggestion.loading .easySuggestion-loading {
            display: block;
        }

        .easySuggestion-list {
            position: absolute;
            border: 1px solid green;
            top: 100%;
            left: 0;
            margin: 0;
            padding: 0;
            list-style: none;
            width: 100%;
        }
        
        .easySuggestion.loading
        .easySuggestion-list{
            display: none
        }

    </style>

    <script> 
        $(document).ready(function () {
            function Suggestion(options) {
                this.options = options;
                this.$input = $(options.input);
                this.$input.wrap('<div class="easySuggestion"></div>');
                this.$wrapper = this.$input.parent();
                this.$ol = $('<ol class="easySuggestion-list"></ol>');
                this.$input.after(this.$ol);
                this.$loading = $('<div class="easySuggestion-loading"></div>');
                this.$loading.html(this.options.loadingTemplate);
                this.$ol.after(this.$loading);
                this.bindEvents();
            };

            Suggestion.prototype.bindEvents = function () {
                var inputObj = this;
                this.$input.on('input', function (e) {
                    inputObj.$wrapper.addClass('loading');
                    inputObj.options.search(e.currentTarget.value, function (array) {
                        inputObj.$wrapper.removeClass('loading');
                        inputObj.$ol.empty();
                        array.forEach(function (text) {
                            inputObj.$ol.append($('<li></li>').text(text));
                        })
                    })
                })
            }


            var s = new Suggestion({
                input: '#suggestion',
                search: function (text, callback) {
                    let array = [];
                    //模拟数据源
                    for (let i = 0; i < 5; i++) {
                        var n = parseInt(Math.random() * 100, 10);
                        array.push(text + n);
                    }
                    //模拟从后台获取数据
                    setTimeout(function () {
                        callback(array)
                    }, 3000)
                },
                loadingTemplate: '<b>加载中</b>'

            })
        })
    </script>



</head>

<body>
    <input type="text" id="suggestion">
</body>

</html>
```
