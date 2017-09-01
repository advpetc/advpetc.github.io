---
layout:     post
title:      "实习学习总结"
subtitle:   "\"知识库扩充\""
date:       2017-07-29 10:57
author:     "Haoyang"
header-img: "img/blackcup-bg.jpg"
catalog:    true
tags:
    总结 索引
---

>总结在实习中学到的知识

## 引

转眼已经到了7月底，我在Atman的实习也正式告一段落。在这短短的两个月内我学到了很多有用的知识，打算分成以下几个类别来总结我学到的知识：

* 网络前端开发(frontend development)
* 网络后端开发(backend development)
* 机器学习(ML)

结合各个领域再细分成具体的内容。

## 网络前端开发

首先要说明的是前端不可能独立存在，如果没有后端提供数据处理是完全没有意义的。我做的是一个web app，目的是为了与人交互然后基于交互目的得到用户想看到的结果。前端在这个部分是直接面向用户的，所以很多时候并不是仅仅靠编程就可以，还要学会构建用户交互逻辑，这个部分更加重要。

**HTML, JS, jQuery**

前端主要的代码都是由这两部分构成的，html负责显示，js负责逻辑。比如要设计一个向上回到顶部的按钮：

```html
<a id="back-to-top" href="#" role="button">Top</a>
```

只用HTML是完全可以做到的，但是在用户看来这只是一个没有任何颜色，只有一行字Top的一个超链接，用户体验十分不好，于是我们添加这些：

```html
<a id="back-to-top" href="#" class="btn btn-primary btn-lg back-to-top pull-right" role="button" data-toggle="tooltip"
   data-placement="right"><span class="glyphicon glyphicon-chevron-up"></span></a>
```

这就变成了一个有颜色和字体的按钮，但是还是不够，我想让它在指定的位置出现而不是一直干扰用户的视线，于是我们就要给它添加一些逻辑：

```javascript
<script>
    $(document).ready(function () {
        $(window).scroll(function () {
            if ($(this).scrollTop() > 50) {
                $('#back-to-top').fadeIn();
            } else {
                $('#back-to-top').fadeOut();
            }


        });
        // scroll body to 0px on click
        $('#back-to-top').click(function () {
            $('#back-to-top').tooltip('hide');
            $('body,html').animate({
                scrollTop: 0
            }, 800);
            return false;
        });

        $('#back-to-top').tooltip('show');

    });

</script>
```

这里使用jQuery的原因由很多，其中就有它可以很好的调用一些函数例如*scroll*和*click*。在实际中我也更加倾向于使用jQuery，简单明了，填充了js本来没有的函数。这里干的事情就是在大于50px的时候显示这个按钮，反之消失，同时在点按的时候加了动画效果。

**一些资源分享**

* [Bootstrap](http://getbootstrap.com/)，现在已经出到第四个版本了，是很棒的基本前端控件的平台。可以本地现在源码，也可以直接用CDN，目前应该还没有被禁。
* [Bootsnipp](https://bootsnipp.com/)，这个是和Bootstrap一起使用的。假如在设计的时候没有好的想法可以从这里找别人写的snipp来模仿，非常有用。
* [jQuery](https://jquery.com/)，这个就不用多说了，我认为最有必要学习的一个前端的语言。下载支持源码也可以用CDN，官网上的google（疑似被墙）microsoft的都可以用。
* [Vue.js](https://vuejs.org/)，如果有时间的话可以用Vue代替js，也是个很好的选择。Vue自己是个js的框架，所以不要盲目使用，要结合自己的需求来做判断。Vue弱化了对js的要求，所以如果你对js不是很熟悉可以从Vue开始熟悉。
* [Material Design](https://getmdl.io/)，这个是Google强推的一个前端设计理念，现在开源了css和js，配合Bootstrap一起用可以提高自己的b格很多。同时也看到有人专门做了相辅相成的[MDB](https://mdbootstrap.com/)，如果懒得自己去设计可以参考这个，但是有部分控件是收费的。

## 网络后端开发

把js放到前端去说的一个原因是我用的Django框架，是一个很容易上手的python框架。后端我主要用python完成，所以只是在迫不得已的情况下才会在前端加一些js的代码。在之前的post里也简单介绍过Django框架，现在再次感受到它的强大，举个例子：

如果你想在本地跑你的app：

```shell
$ ./manage.py runserver
```

默认在127.0.0.1:8000，假如想在同个局域网给朋友showoff，只要在runserver后添加你的ip地址和对应的端口就可以了：

```shell
$ ./manage.py runserver YOUR_IP_ADDRESS:PORT
```

假如你要更改自己的model，只要在改完之后使用如下指令：

```shell
$ ./manage.py migrate
$ ./manage.py makemigrations
```
不再需要一个一个检查model和之前有什么区别，因为Django已经帮你比较好了，你只要按照流程更改就可以了，如果运气好直接migrate就不会用问题了。

Django的setting.py文件相当于你的config文件，尤其的重要，部署的时候添加机器的ip到ALLOWED_DOMAIN，再设置好数据库就可以了在server上跑了（当然你要先把环境用docker设好）。Django支持的数据库真的是一大把，小学生级别的设置，很多时候填用户名和密码就可以了，剩下Django都帮你设置好了。不过在迁移数据库的时候（尤其从sqlite到postgresql）Django是把文件dump出来然后再写入，所以难免由很多migrations的问题要解决，关于迁移数据库做了一个简单的总结：

```shell
$ python manage.py migrate --database=postgresql --run-syncdb
//python manage.py dumpdata --all --indent=4 -e auth.user -e admin.logentry -e sessions -e=contenttypes  > imtx.json
//python manage.py dumpdata > dump.json
$ python manage.py loaddata imtx.json --database=postgresql
```

里面comment掉的是dump的两个方法，第一个是去除了一些不必要的auth，session的行，第二个是直接dump，我觉得既没有意义也总是出现migrations conflict的问题，第一个就当作一个不错的snipp记录下来吧。

用python写后端确实轻松一些，但是还是有很多要注意的情况，比如处理url等，这些在Django的官方教程中其实都可以看到，我在这里就不重复了。

**一些资源分享**

* [Django](djangoproject.com)，官方给了一个很详细的教程，做的是一个投票系统，MVC框架在看完这个教程之后就基本了解了。
* [Node.js](https://nodejs.org/en/)，这次实习我自己没有用node，不过看到别人用的时候感觉很酷，尤其是移步进程的部分非常值得学习，可以提升网页速度，这里稍微提一下debug的工具[node-inspector](https://github.com/node-inspector/node-inspector)，用`$ npm install -g node-inspector`就安装好了。
* 关于数据库我整理了一个从备份postgres数据库的脚本，在[Gist](https://gist.github.com/advpetc/9ec48862fa2f64ed5505cb39c47a56ec)上分享。里面需要了解以下CronTab的知识。
* [Postgres](https://www.postgresql.org/)，这个数据库由有很多支持的第三方客户端，我比较喜欢的是在Mac上的[Posgres.app](http://postgresapp.com/)，一键式的启动，不过在具体操作的时候还是用的JetBrain的[DataGrip](https://www.jetbrains.com/datagrip/)，这里顺便提一下JetBrain免费提供学生使用，所以如果是学生强推直接升级的professional版，我用的PyCharm在升级之后发现可以直接调用数据库，非常酸爽。

## 机器学习部分

这部分主要是我自己琢磨的比较多，在数据方面做了一些数据清理的事情。因为是NLP公司，所以在处理分词上我还做了一些事情，后来居然发现了nltk的一个bug，现在已经提交了[issue](https://github.com/nltk/nltk_data/issues/85)，有兴趣的旁友可以看一下。在清理这部分公司用了自己的运行环境，主要使用一些自定义的schema来给不同类型的数据做测试。举一个例子：

```python
class General_Tokenizer(Rule_File_Shell):
    def __init__(self, **kwargs):  # delimiter should be singlself.exe, e char
        super(self.__class__, self).__init__()
        self.set_desc('File Shell cmd: general tokenizer (same as in Moses)', self)
        self.lang = 'en'
        if 'lang' in kwargs: self.lang = kwargs['lang']
        self.exe = os.path.join(os.path.dirname(os.path.abspath(sys.argv[0])), 'tokenizer', 'tokenizer.perl')

    def run(self, files):
        file = files[0]
        self.file_exist(file)
        cmd = r"chmod 755 %s; %s -l %s < %s > %s" % (self.exe, self.exe, self.lang, file, self.output)
        self.execute_cmd(cmd)
        return [self.output]
```

这里继承了运行的Rule_File_Shell，的对象，在这里直接跑就可以了。同时在tokenize的时候调用了一个perl的脚本来进行，脚本可以在[这里](https://github.com/moses-smt/mosesdecoder/blob/master/scripts/tokenizer/tokenizer.perl)看到。这部分的操作实际上十分简单，但是一个一定要有一个好的架构才可以把复杂的事情简单化。

最后讲一下ML的部分。我在初步学习了一下Tensorflow和Stanford231的课之后整理了一些资源和笔记，我认为ML不是一两句话可以说清楚的，跟何况自己也是刚刚入门，就先不发表自己的拙见。
* Computer Vision学习的路线可以参照Stanford231的课程，从环境setup开始一直到后面的KNN, Softmax, Linear Regression等等。
* 工具的使用也很重要，我建议使用google的Tensorflow来把自己的模型形象成一个可观的图，使用[TensorBoard](https://www.tensorflow.org/get_started/summaries_and_tensorboard)会十分有帮助。
* NLTK部分可以从[感知器](https://zybuluo.com/hanbingtao/note/433855)开始，然后学习[线性单元和梯度下降](https://zybuluo.com/hanbingtao/note/448086)，然后学习[神经网络和反向传播算法](https://zybuluo.com/hanbingtao/note/476663)，然后学习[卷积神经网络](https://zybuluo.com/hanbingtao/note/485480)，这四个其实是一个系列，讲的很清楚。还有一些神经系统相关，在这里也分享一下：
    * [RUN](http://karpathy.github.io/2015/05/21/rnn-effectiveness/)
    * [LSTM](http://www.jianshu.com/p/9dc9f41f0b29)
    * [GRU](https://arxiv.org/pdf/1406.1078v3.pdf)

总之在我看来要想做好ML首先要有足够的数据，然后配合一个强有力的cpu和gpu加速就可以做成想要的东西，这部分东西我还有很多要学习的地方。

## Bounces Point

好吧，这里最后说一下翻墙的事情。我用的[Vultr](http://www.vultr.com/)，然后各位旁友想要优惠可以点击下面👇这个链接使用我的refer

<a href="https://www.vultr.com/?ref=7171668"><img src="https://www.vultr.com/media/banner_1.png" width="728" height="90"></a>

原理其实很简单，就是在别的国家使用他们的电脑代理你的电脑，这样你的电脑的网络就相当于是他们地区的了。我建议使用日本的代理，因为离我们近而且速度很快。如果用vultr的话一个月顶多1t的流量就够了，而且这才五刀，如果没用满还是按rate计价，十分的良心。这里具体的翻墙教程请参见[科学上网的终极姿势-在-vultr-vps-上搭建-shadowsocks](https://medium.com/@zoomyale/%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91%E7%9A%84%E7%BB%88%E6%9E%81%E5%A7%BF%E5%8A%BF-%E5%9C%A8-vultr-vps-%E4%B8%8A%E6%90%AD%E5%BB%BA-shadowsocks-fd57c807d97e)这篇文章，写的超级详细，真正的小白也能看懂。

--Haoyang
