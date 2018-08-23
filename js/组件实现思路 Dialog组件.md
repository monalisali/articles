
## 先定义接口
首先，我们设想一下用户（其他程序员）如何使用这个Dialog组件比较方便。

可能是这样使用的
```
button.onclick = function(){
  var dialog = new Dialog({
    title: '标题',
    content: '<b>欢迎<b>',
    buttons:[
      {text: '确定',action:function(){
            setTimeout(()=>{
                dialog.close();
            })
      }},
      
      {text: '取消',action:function(){dialog.close();}},
    ]
    
  });
  
  dialog.open(); //dialog.close();
}

```
Dialog组件会提供一些列属性让用户可以自定义弹出框的UI，按钮，按钮的callback等。*content属性明显是违反正交原则的，但是为了组件的便利性，只能妥协了*。**而且一般来说，像content这种允许用户自定义html的属性，都要预防注入攻击：比如在html里面插入<script>。 所以，组件代码里要过滤掉这些标签**
 
同时，open和close函数还是有必要提供的。


## 组件封装

#### 依赖
Jquery

#### 接口
- title: Dialog的title
- content: Dailog主体显示内容
- className：用户给Dialog自定义的class
- buttons：Dialog的按钮定义，是一个数组。数组接受的对象为：{text:'按钮名称', action: 按钮执行的函数}; 
- open: 打开窗口
- close: 关闭窗口

### 代码

#### CSS


```

    .easyDialog {
      background: rgba(0, 0, 0, 0.5);
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    .easyDialog-wrapper {
      background-color: white;
      padding: 20px;
      min-width: 200px;

    }

    .easyDialog-header {
      border-bottom: 1px solid;
    }

    .easyDialog-main {
      padding-top: 10px;
      padding-bottom: 10px;
    }

    .easyDailog-footer {
      margin-left: 10px
    }

```

#### JS
```
      function Dialog(options) {
        this.options = options;
        this.init();
      }

      Dialog.prototype.init = function () {
        var $buttons = this.options.buttons.map(function (btOption) {
          var $b = $('<button></button>');
          $b.text(btOption.text);
          $b.on('click', btOption.action);
          return $b;
        });

        var template = "<div class='easyDialog'>"
          + "<div class='easyDialog-wrapper'>"
          + "<header class='easyDialog-header'>" + this.options.title + "</header>"
          + "<main class='easyDialog-main'>" + this.options.content + "</main>"
          + "<footer class='easyDailog-footer'></footer>"
          + "</div>"
          + "</div>";

        var $dialog = $(template);
        $dialog.find('footer').append($buttons);
        $dialog.addClass(this.options.className);
        this.$dialog = $dialog;
      }

      Dialog.prototype.open = function () {
        this.$dialog.appendTo('body');
      }

      Dialog.prototype.close = function () {
        this.$dialog.detach();
      }
      
```

## 使用
#### html

```
<button id="bt">点击</button>
```

#### JS
```
  $("#bt").click(function () {
        var dialog = new Dialog({
          title: '标题',
          content: '<b>欢迎</b>', //自定义窗口的内容
          className: 'userDialog', //自定义窗口样式
          buttons: [
            {
              text: '确定', action: function () {
                  setTimeout(function(){
                      dialog.close();
                  },3000)
              }
            },

            { text: '取消', action: function () { dialog.close(); } },
          ]
        })

        dialog.open();

      });

```
  
 
