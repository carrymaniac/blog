# Vue的基础学习


## vue指令
- v-text：设置替换普通文本，和{{ }}效果类似：
```html
    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>vue</title>
</head>
<body>
    <div id="app">
        <h2 id="data">这是一个信息:{{message}}</h2>
        <div v-text="info"></div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
            var app = new Vue({
                el:"#app",
                data:{
                    message:"这是gdou",
                    info:"这是info"
                }
            })
    </script>
</body>
</html>
```

- v-html :用于拼接html代码块，与v-text有所区别
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>vue</title>
</head>
<body>
    <div id="app">
        <h2 id="data">这是一个信息:{{message}}</h2>
        <div v-text="info"></div>
        <div v-html="htmlcode"></div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
            var app = new Vue({
                el:"#app",
                data:{
                    message:"这是gdou",
                    info:"这是info",
                    htmlcode:"<a href='http://www.baidu.com' target='_blank' rel='noopener noreferrer'>百度一哈？</a>"
                }
            })
    </script>
</body>
</html>
```


- v-on ：绑定事件
其中有v-on:click="doit"(单击事件)
v-on:monseenter="doit"(鼠标进入事件)
v-on:dblclick="doit"(鼠标双击事件)
其中，可以使用@来替换v-on,即使用@dblclick来实现同样的功能,在vue方法中可以使用this.XX来操作数据

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Vue-learn</title>
</head>
<body>
    <div id="app">
            <div v-text="food"></div>
            <input type="button" value="v-on" v-on:click="doIt">
            <input type="button" value="v-on for @" @click="change">
            <input type="button" value="v-on for double" @dblclick="doItdbl">
    </div>

        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
        <script>
            var app = new Vue({
                el:"#app",
                data:{
                    food:"西兰花🥦"
                },
                methods:{
                    doIt:function(){
                        alert("just do it")
                    },
                    doItdbl:function(){
                        alert("just do it for double ")
                    },
                    change:function(){
                        console.log(this.food)
                        this.food = "番茄🍅炒鸡蛋🥚"
                    }
                }
            })
        </script>
</body>
</html>
```


## 计数器
代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>

    <div id="app">
    <div class="input-num">
        <!-- @绑定方法 -->
            <button @click="sub">-</button>
            <span>{{number}}</span>
            <button @click="add">+</button>
    </div>
    </div>


    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            // el 设置挂载点
            el:"#app",
            data:{
                number:1
            },
            methods:{
                add:function(){
                    this.number = this.number+1
                },
                sub:function(){
                    if(this.number>0){
                        this.number = this.number-1
                    }else{
                        alert("最小啦，别点了")
                    }
                }
            }
        })
    </script>
</body>
</html>
```

- v-show指令
指定元素是否显示,可以使用表达式、布尔值、变量,代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
            <!-- 布尔值变量 -->
            <img v-show="isShow" src="https://image.gcores.com/f3a8d8c6-7921-406b-8db8-675787f04340.jpg" alt="极乐迪斯科" srcset="">
            <!-- 表达式 -->
            <img v-show="age>22" src="https://image.gcores.com/f3a8d8c6-7921-406b-8db8-675787f04340.jpg" alt="极乐迪斯科" srcset="">
            <button @click="changeIsShow">change is show</button>
            <button @click="changeAge">change age</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                isShow:true,
                age:19
            },
            methods:{
                changeIsShow:function(){
                    this.isShow = !this.isShow
                },
                changeAge:function(){
                    this.age++;
                }
            }
        })
    </script>
</body>
</html>
```


### v-if
v-if和v-show基本上差不多，和v-show不同的是，v-show不显示的时候是修改了对象的dispaly的属性(display:none),而if是直接将对象在dom树中删除，在实际代码中，如果该元素频繁切换，则使用v-show，因为频繁操作dom树的性能代价较大。而如果不经常切换的元素，则使用v-if即可

### v-bind
用于修改元素的属性：例如：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .active{
            border: 1px solid red;
        }
    </style>
</head>
<body>
    <div id="app">
            <img :src="imgSrc" :tittle="imgTittile+'!!!'" :class="isActive?'active':''">
            <img v-bind:tittle="imgTittile+'!!!'">
            <img v-bind:class="isActive?'active':''">
            <img v-bind:class="{active:isActive}">
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                imgSrc:"https://image.gcores.com/f3a8d8c6-7921-406b-8db8-675787f04340.jpg",
                imgTittile:"极乐迪斯科",
                isActive:true
            },
            methods:{
                changeIsShow:function(){
                    this.isShow = !this.isShow
                },
                changeAge:function(){
                    this.age++;
                }
            }
        })
    </script>
</body>
</html>
```
其中，v-bind 可以省略为,即使用:

### 轮播图
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .active{
            border: 1px solid red;
        }
    </style>
</head>
<body>
    <div id="app">
           <img :src="imgArr[index]" alt="" srcset="">
           <a href="#" @click="prev" v-show="index!=0">上一张</a>
           <a href="#" @click="next" v-show="index<imgArr.length-1">下一张</a>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                imgArr:["./img/image-1.png"
                ,"./img/image-2.png"
                ,"./img/image-3.png"
                ,"./img/image-4.png"
                ,"./img/image-5.png"
            ],
                index:0
            },
            methods:{
                prev:function(){
                    if(this.index>0){
                        this.index--;
                        console.log(this.index)
                    }else{
                        this.index = 0;
                    }
                },
                next:function(){
                    if(this.index<4){
                        this.index++;
                        console.log(this.index)
                    }else{
                        this.index=4;
                    }
                }
            }
        })
    </script>
</body>
</html>
```

