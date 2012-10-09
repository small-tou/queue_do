queue_do
========

异步队列同步化，支持递归嵌套

基于nodejs模块封装，同样适用于前端的异步同步化。

#安装

<pre>
npm install queuedo
</pre>

#一个例子，递归遍历某个文件夹下所有的图片并压缩到1000宽度。

<pre>
var fs   = require('fs');
var path = require('path');
//源文件
var source = '/Applications/XAMPP/xamppfiles/htdocs/htmljs';
//目标文件
var target = '/Applications/XAMPP/xamppfiles/htdocs/htmljs_temp';
//引入gm做图片处理器
var gm = require('gm');
var imageMagick = gm.subClass({
    imageMagick: true
});
var queue_do=require("./queue_do.js")
var walk=function(_path,callback,next_func){
    var list=[]
    if(fs.existsSync(_path)){
        var stat=fs.statSync(_path)
        if(stat.isDirectory()){
            fs.readdir(_path,function(error,files){
                queue_do(files,function(__path,next,context){
                    walk(_path+"/"+__path,callback,function(){
                        next.call(context)
                    })
                },function(){
                    next_func()
                })
            })
        }else{
            callback(_path)
            next_func()
        }
    }
}
walk(source,function(list){
    console.log(list)
},function(){
    console.log("all finish!")
})

</pre>