# 献给所有想在Github.io中想写博文的人
###（摘抄于https://blog.csdn.net/qibin0506/article/details/51813428）
### 踉踉跄跄折腾终于搞定了，感谢雨燕姐姐的牛腿，下面对整个博文的发布流程和原理进行分析，不足或者错误之处还请指正。
#准备工作
### 首先要有自己的github账号，然后创建一个新的项目：![Smaller icon](http://lilailong.github.io/picture/1.png) ，然后我们将该项目拷贝到本机，命令git clone 项目所在地址,例如我的命令![Smaller icon](http://lilailong.github.io/picture/2.png) 
### 那么本地就有了github该项目的一个副本了。

# 开始搭建自己的博客
下图为搭建博客的原理

 <img src="http://lilailong.github.io/picture/3.png" style="align:center;height:200px;width:300px;" />

从上图可以看出，如果想通过域名访问lilailong.github.io，那么在url中输入*.github.io后，Github的服务器首先返回的储存在线上Github的index.html文件，该文件的作用整个显示分页、调用css、js，所以index.html不用自己写，直接下载即可。也就是说在对域名访问之前，我们应该在线上github中准备好这些文件。
那么接下的事情，就是在本机的github仓库中准备好这些文件，再同步到线上那么用户就可以通过域名看到我们的博客了。
原理，毕。
#正式开写


* 准备工具－－TitanPages软件可以帮助我们完成上述操作，该下载地址<https://github.com/qibin0506/TitanPages>进行下载，Linux用户直接下载tt文件，该tt文件就是一个执行文件，将tt文件复制到项目下，直接运行./tt即可 Mac(可能现在还存在bug我试了几次，tt不能直行成功)和Window同理。

* 文件准备－－我们还需要准备index.html索引文件，content.html渲染模板，js文件包、css文件包、fonts包，可以在<https://github.com/lilailong/lilailong.github.io>中进行下载，一定要看好了，只有这几个包需要下载，其他的不用。

准备好了我们的工具tt、几个支持的包，那么我们的创建博文工作便可以开始了。
这时我们的文件目录应该如下：
![Mou icon](http://lilailong.github.io/picture/4.png)

首先，我们要生成一个博文：

1. 生成最初使的md文件
执行命令:tt -type create -file test
这是我们会发现多了个文件夹raw中多出来一个test.md的文件，这也是我们今后写博文的文件，我们的博文都是通过markdown格式进行编写然后发布的。
2. 将生成的md文件用./content.html模板渲染成html命令如下：

   tt -type build -file test -author 作者 -tmpl ./content.html
3. 使用tt -type cate就可以完成目录的自动创建(如果此时显示html中的category.auto.js已经存在，要强行覆盖，因为这个文件是上一次的目录文件)
4. 同步到线上Github

	git add -A
	
    git commit -m "**"
    
	git push origin master -f
5. 今后第二篇时候，就回到第一步，tt -type create -file test2然后一次往下执行。

  
