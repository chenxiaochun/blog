![](http://img13.360buyimg.com/uba/jfs/t5251/135/2599843554/16521/36cdb87/591bde69Nff73e731.png)

我们平时一般使用`setTimeout`函数的姿势是这样的：
```javascript
setTimeout(function(){
    console.log(1);
}, 2000)
```
一般情况下，过2秒一定会输出结果：`1`。可是你知道`setTimeout`还可以传入第三个参数吗，就像这样：
```javascript
setTimeout(function(n){
    console.log(n)
}, 2000, 1)
```
是的，过2秒之后的输出结果还是：`1`。那么这个小技巧能用在什么场景呢？别急，接着往下看。我们大概都见过以下的代码逻辑：
```javascript
for(var i=0; i<5; i++){
    setTimeout(function(){
        console.log(i);
    }, 200);
}
```
大家都能猜出运行结果，它会输出五个数字`5`：
```
5
5
5
5
5
```
可是，我想让它顺序输出每个变量`i`的值，应该怎么写呢？是的，有人说用一个自执行的匿名函数，这也是非常流行的一个解决办法，就像这样：
```javascript
for(var i=0; i<5; i++){
    (function(n){
        setTimeout(function(){
            console.log(n);
        }, 200);
    })(i); 
}
```
输出结果也毫无疑问：
```
0
1
2
3
4
```
还有一种更简单的方式，就是利用上面提到的`setTimeout`的第三个参数，所以代码可以写成这样：
```javascript
for(var i=0; i<5; i++){
    setTimeout(function(n){
        console.log(n);
    }, 200, i);
}
```
```
0
1
2
3
4
```
输出结果和上面完全一样。同理，`setInterval`函数同样可以这样使用。
