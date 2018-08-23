
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


## 封装组件

### 接口
- input: 根据用户提供的选择器对input元素进行初始化
- search(text,callback): 用户通过text来提供suggestion数据，并把这个列表传递给callback
   - text: input中输入的内容
   - callback:组件通过这个callback来绘制suggestion列表
- loadingTemplate: 数据加载时显示的内容
- emptyTemplate: 没有suggestion数据时显示的内容

### 注意点
一定要做函数节流处理。不然，每在input中输入一下内容，就会发送一次请求。


#### js

```
function Suggestion(options) {
                this.options = options;
                this.$input = $(options.input);
                this.$input.wrap('<div class="easySuggestion"></div>');
                this.$wrapper = this.$input.parent();
                this.$ol = $('<ol class="easySuggestion-list"></ol>');
                this.$input.after(this.$ol);
                this.$loading = $('<div class="easySuggestion-loading"></div>');
                this.$loading.html(this.options.loadingTemplate);
                this.$empty = $('<div class="easySuggestion-empty"></div>');
                this.$empty.html(this.options.emptyTemplate);
                this.$ol.after(this.$loading);
                this.$ol.after(this.$empty);
                this.bindEvents();
            };

            Suggestion.prototype.bindEvents = function () {
                var timerId;
                var inputObj = this;
                this.$input.on('input', function (e) {
                    //!!一定要做函数节流，否则每输入一次都会发送请求
                    if (timerId) {
                        window.clearTimeout(timerId);
                    }
                    timerId = setTimeout(function () {
                        inputObj.search(e.currentTarget.value, inputObj);
                        timerId = undefined;
                    }, 1000);

                })
            }

            Suggestion.prototype.search = function (keywords, inputObjValue) {
                this.$wrapper.addClass('loading');
                this.options.search(keywords, function (array) {
                    inputObjValue.$wrapper.removeClass('loading');
                    inputObjValue.$ol.empty();
                    //没有数据时显示emptyTemplate   
                    if (!array || array.length === 0) {
                        inputObjValue.$wrapper.addClass('empty');
                        return;
                    }

                    array.forEach(function (text) {
                        inputObjValue.$ol.append($('<li></li>').text(text));
                    })
                })
            }
```

#### css

```
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
        /*.loading相当于一个状态控制。一旦easySuggestion上加了class loading, 那么加载内容的class easySuggestion-loading就要显示出来*/

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


        .easySuggestion.loading .easySuggestion-list {
            display: none
        }
        /*.empty状态控制*/

        .easySuggestion.empty .easySuggestion-empty {
            display: block;
        }

        .easySuggestion-empty {
            display: none;
        }
```

### 组件使用
#### html
```
<input type="text" id="suggestion">
```


#### js
```
<input type="text" id="suggestion">
```

