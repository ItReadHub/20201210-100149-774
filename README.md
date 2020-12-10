# 抖音爬虫教程，就想写个爬虫，我到底要学多少东西啊？

当今大数据的时代，网络爬虫已经成为了获取数据的一个重要手段。<br>但要学习好爬虫并没有那么简单。首先知识点和方向实在是太多了，它关系到了计算机网络、编程基础、前端开发、后端开发、App 开发与逆向、网络安全、数据库、运维、机器学习、数据分析等各个方向的内容，它像一张大网一样把现在一些主流的技术栈都连接在了一起。正因为涵盖的方向多，因此学习的东西也非常零散和杂乱，很多初学者搞不清楚究竟要学习哪些知识，学习过程中遇到反爬也不知道用什么方法来解决，本篇我们来做一些归纳和总结。

## 初学爬虫
一些最基本的网站，往往不带任何反爬措施。比如某个博客站点，我们要爬全站的话就顺着列表页爬到文章页，再把文章的时间、作者、正文等信息爬下来就可以了。<br>那代码怎么写呢？用 Python 的 requests 等库就够了，写一个基本的逻辑，顺着把一篇篇文章的源码获取下来，解析的话用 XPath、BeautifulSoup、PyQuery 或者正则表达式，或者粗暴的字符串匹配把想要的内容抠出来，再加个文本写入存下来就完事了。<br>代码很简单，就几个方法调用。逻辑很简单，几个循环加存储。最后就能看到一篇篇文章就被我们存到自己的电脑里面了。当然有的同学可能不太会写代码或者都懒得写，那么利用基本的可视化爬取工具，如某爪鱼、某裔采集器也能通过可视化点选的方式把数据爬下来。<br>如果存储方面稍微扩展一下的话，可以对接上 MySQL、MongoDB、Elasticsearch、Kafka 等等来保存数据，实现持久化存储。以后查询或者操作会更方便。<br>反正，不管效率如何，一个完全没有反爬的网站用最最基本的方式就搞定了。<br>到这里，你就说你会爬虫了吗？不，还差的远呢。

## Ajax、动态渲染
随着互联网的发展，前端技术也在不断变化，数据的加载方式也不再是单纯的服务端渲染了。现在你可以看到很多网站的数据可能都是通过接口的形式传输的，或者即使不是接口那也是一些 JSON 的数据，然后经过 JavaScript 渲染得出来的。<br>这时候，你要再用 requests 来爬那就不顶用了，因为 requests 爬下来的源码是服务端渲染得到的，浏览器看到页面的和 requests 获取的结果是不一样的。真正的数据是经过 JavaScript 执行的出来的，数据来源可能是 Ajax，也可能是页面里的某些 Data，也可能是一些 ifame 页面等等，不过大多数情况下可能是 Ajax 接口获取的。<br>所以很多情况下需要分析 Ajax，知道这些接口的调用方式之后再用程序来模拟。但是有些接口带着加密参数，比如 token、sign 等等，又不好模拟，咋整呢？<br>一种方法就是去分析网站的 JavaScript 逻辑，死抠里面的代码，揪出来这些参数是怎么构造的，找出思路来了之后再用爬虫模拟或重写就行了。如果你解出来了，那么直接模拟的方式效率会高非常多，这里面就需要一些 JavaScript 基础了，当然有些网站加密逻辑做的太牛逼了，你可能花一个星期也解不出来，最后放弃了。<br>那这样解不出来或者不想解，那咋办呢？这时候可以有一种简单粗暴的方法就是直接用模拟浏览器的方式来爬取，比如用 Puppeteer、Pyppeteer、Selenium、Splash 等，这样爬取到的源代码就是真正的网页代码，数据自然就好提取了，同时也就绕过分析 Ajax 和一些 JavaScript 逻辑的过程。这种方式就做到了可见即可爬，难度也不大，同时模拟了浏览器，也不太会有一些法律方面的问题。<br>但其实后面的这种方法也会遇到各种反爬的情况，现在很多网站都会去识别 webdriver，看到你是用的 Selenium 等工具，直接干掉或不返回数据，所以你碰到这种网站还得来专门解一下这个问题。

## 多进程、多线程、协程
上面的情况如果用单线程的爬虫来模拟是比较简单的，但是有个问题就是速度慢啊。<br>爬虫是 IO 密集型的任务，所以可能大多数情况下都在等待网络的响应，如果网络响应速度慢，那就得一直等着。但这个空余的时间其实可以让 CPU 去做更多事情。那怎么办呢？多开点线程吧。<br>所以这时候我们就可以在某些场景下加上多进程、多线程，虽然说多线程有 GIL 锁，但对于爬虫来说其实影响没那么大，所以用上多进程、多线程都可以成倍地提高爬取速度，对应的库就有 threading、multiprocessing 了。<br>异步协程就更牛逼了，用 aiohttp、gevent、tornado 等等的基本上你想搞多少并发就搞多少并发，但是还是悠着点，别把人家网站搞挂了。<br>总之，用上这几个，爬虫速度就提上来了。<br>但速度提上来了不一定是好事，反爬接着肯定就要来了，封你 IP、封你账号、弹验证码、返回假数据，所以有时候龟速爬似乎也是个解决办法？

## 分布式
多线程、多进程、协程都能加速，但终究还是单机的爬虫。要真正做到规模化，还得来靠分布式爬虫来搞。<br>分布式的核心是什么？资源共享。比如爬取队列共享、去重指纹共享等等。<br>我们可以使用一些基础的队列或组件来实现分布式，比如 RabbitMQ、Celery、Kafka、Redis 等等，但经过很多人的尝试，自己去实现一个分布式爬虫，性能和扩展性总会出现一些问题，当然特别牛逼的除外哈。不少企业内部其实也有自己开发的一套分布式爬虫，和业务更紧密，这种当然是最好了。<br>现在主流的 Python 分布式爬虫还是基于 Scrapy 的，对接 Scrapy-Redis、Scrapy-Redis-BloomFilter 或者用 Scrapy-Cluster 等等，他们都是基于 Redis 来共享爬取队列的，总会多多少少遇到一些内存的问题。所以一些人也考虑对接到了其他的消息队列上面，比如 RabbitMQ、Kafka 等等，解决一些问题，效率也不差。<br>总之，要提高爬取效率，分布式还是必须要掌握的。

## 验证码
爬虫难免遇到反爬，验证码就是其中之一。要会反爬，那首先就要会解验证码。<br>现在你可以看到很多网站都会有各种各样的验证码了，比如最简单的图形验证码，要是验证码的文字规整的话，OCR 过一遍或者基本的模型库都能识别，不想搞这个的话可以直接去对接个打码平台来搞，准确率还是有的。<br>然而你可能现在都见不到什么图形验证码了，都是一些行为验证码，如某验、某盾等等，国外也有很多，比如 reCaptcha 等等。一些稍微简单一点的，比如滑动的，你可以找点办法识别缺口，比如图像处理比对、深度学习识别都是可以的。轨迹呢自己写个模拟正常人行为的，加点抖动之类的。有了轨迹之后咋模拟呢，如果你牛逼，那么可以直接去分析验证码的 JavaScript 逻辑，把轨迹数据录入，那就能得到里面的一些加密参数，直接拿着这些参数放到表单或接口里面就能直接用了。当然也可以用模拟浏览器的方式来拖动，也能通过一定的方式拿到加密参数，或者直接用模拟浏览器的方式把登录一起做了，拿着 Cookies 来爬也行。<br>当然拖动只是一种验证码，还有文字点选、逻辑推理等，要是真不想搞，可以找打码平台来解出来再模拟，但毕竟花钱的，一些高手就会选择自己训练深度学习相关的模型，收集数据、标注、训练，针对不同的业务训练不同的模型。这样有了核心技术，也不用再去花钱找打码平台了，再研究下验证码的逻辑模拟一下，加密参数就能解出来了。不过有的验证码难得很，有的我也没搞定。<br>当然有些验证码可能是请求过于频繁而弹出来的，这种如果换个 IP 什么的也能解。

## 封 IP
封 IP 也是个令人头疼的事，行之有效的方法就是换代理了。<br>代理很多种，市面上免费的，收费的太多太多了。<br>首先可以把市面上免费的代理用起来，自己搭建一个代理池，收集现在全网所有的免费代理，然后加一个测试器一直不断测试，测试的网址可以改成你要爬的网址。这样测试通过的一般都能直接拿来爬你的目标网站。我自己也搭建过一个代理池，现在对接了一些免费代理，定时爬、定时测，还写了个 API 来取，放在 GitHub 了：[https://github.com/Python3WebSpider/ProxyPool](https://github.com/Python3WebSpider/ProxyPool)，打好了 Docker 镜像，提供了 Kubernetes 脚本，大家可以直接拿来用。<br>付费代理也是一样，很多商家提供了代理提取接口，请求一下就能获取几十几百个代理，我们可以同样把它们接入到代理池里面。但这个代理也分各种套餐，什么开放代理、独享代理等等的质量和被封的几率也是不一样的。<br>有的商家还利用隧道技术搭了代理，这样代理的地址和端口我们是不知道的，代理池是由他们来维护的，比如某布云，这样用起来更省心一些，但是可控性就差一些。<br>还有更稳定的代理，比如拨号代理、蜂窝代理等等，接入成本会高一些，但是一定程度上也能解决一些封 IP 的问题。<br>不过这些背后也不简单，为啥一个好好的高匿代理就是莫名其妙爬不了，背后的一些事就不多讲了。<br>##封账号<br>有些信息需要模拟登录才能爬嘛，如果爬的过快，人家网站直接把你的账号封禁了，就啥都没得说了。比如爬公众号的，人家把你 WX 号封了，那就全完了。<br>一种解决方法当然就是放慢频率，控制下节奏。<br>还有种方法就是看看别的终端，比如手机页、App 页、wap 页，看看有没有能绕过登录的法子。<br>另外比较好的方法，那就是分流。如果你号足够多，建一个池子，比如 Cookies 池、Token 池、Sign 池反正不管什么池吧，多个账号跑出来的 Cookies、Token 都放到这个池子里面，用的时候随机从里面拿一个。如果你想保证爬取效率不变，那么 100 个账号相比 20 个账号，对于每个账号对应的 Cookies、Token 的取用频率就变成原来的了 1/5，那么被封的概率也就随之降低了。

## 奇葩的反爬
上面说的是几种比较主流的反爬，当然还有非常多奇葩的反爬。比如返回假数据、返回图片化数据、返回乱序数据、返回骂人的数据、返回求饶的数据，那都具体情况看着办吧。<br>这些反爬也得小心点，之前见过一个反爬直接返回 `rm -rf /` 的也不是没有，你要是正好有个脚本模拟执行返回结果，后果自己想象哈。

## JavaScript 逆向
说到重头了。随着前端技术的进步和网站反爬意识的增强，很多网站选择在前端上下功夫，那就是在前端对一些逻辑或代码进行加密或混淆。当然这不仅仅是为了保护前端的代码不被轻易盗取，更重要的是反爬。比如很多 Ajax 接口都会带着一些参数，比如 sign、token 等等，这些前文也讲过了。这种数据我们可以用前文所说的 Selenium 等方式来爬，但总归来说效率太低了，毕竟它模拟的是网页渲染的整个过程，而真实的数据可能仅仅就藏在一个小接口里。<br>如果我们能够把一些接口的参数真正找出其中的逻辑，用代码来模拟执行，那效率就会有成倍的提升，而且还能在一定程度上规避上述的反爬现象。<br>但问题是什么？难啊。<br>Webpack 是一方面，前端代码都被压缩和转码成一些 bundle 文件，一些变量的含义已经丢失，不好还原。然后一些网站再加上一些 obfuscator 的机制，把前端代码变成你完全看不懂的东西，比如字符串拆散打乱、变量十六进制化、控制流扁平化、无限 debug、控制台禁用等等，前端的代码和逻辑已经面目全非。有的用 WebAssembly 等技术把前端核心逻辑直接编译，那就只能慢慢抠了，虽然说有些有一定的技巧，但是总归来说还是会花费很多时间。但一旦解出来了，那就万事大吉了。怎么说？就像奥赛题一样，解出来升天，解不出来 GG。<br>很多公司招聘爬虫工程师都会问有没有 JavaScript 逆向基础，破解过哪些网站，比如某宝、某多、某条等等，解出来某个他们需要的可能就直接录用你。每家网站的逻辑都不一样，难度也不一样。

## App
当然爬虫不仅仅是网页爬虫了，随着互联网时代的发展，现在越来越多的公司都选择将数据放到 App 上面，甚至有些公司只有 App 没有网站。所以数据只能通过 App 来爬。<br>咋爬呢？基本的就是抓包工具了，Charles、Fiddler 一把梭，抓到接口之后，直接拿来模拟就行了。<br>如果接口有加密参数怎么办呢？一种方法你可以边爬边处理，比如 mitmproxy 直接监听接口数据。另一方面你可以走 Hook，比如上 Xposed 也可以拿到。<br>那爬的时候又怎么实现自动化呢？总不能拿手来戳吧。其实工具也多，安卓原生的 adb 工具也行，Appium 现在已经是比较主流的方案了，当然还有其他的某精灵都是可以实现的。<br>最后，有的时候可能真的就不想走自动化的流程，我就想把里面的一些接口逻辑抠出来，那就得搞逆向了，IDA Pro、jdax、FRIDA 等工具就派上用场了，当然这个过程和 JavaScript 逆向一样很痛苦，甚至可能得读汇编指令。搞一个案例掉一把头发也不是不可能的。

## 智能化
上面的这一通，都搞熟了，恭喜你已经超过了百分之八九十的爬虫玩家了，当然专门搞 JavaScript 逆向、App 逆向的都是站在食物链顶端的男人，这种严格来说已经不算爬虫范畴了，这种神我们就不算在里面了，反正我不是。<br>除了上面的一些技能，在一些场合下，我们可能也需要结合一些机器学习的技术，让我们的爬虫变得更智能起来。<br>比如现在很多博客、新闻文章，其页面结构相似度比较高，要提取的信息也比较类似。<br>比如如何区分一个页面是索引页还是详情页？如何提取详情页的文章链接？如何解析文章页的页面内容？这些其实都是可以通过一些算法来计算出来的。<br>所以，一些智能解析技术也营运而生，比如提取详情页，一位朋友写的 GeneralNewsExtractor 表现就非常好。<br>假如说我来了一个需求，我要爬取一万个新闻网站数据，要一个个写 XPath 吗？写死我吧。如果有了智能化解析技术，在容忍一定错误的条件下，完成这个就是分分钟的事情。<br>总之，如果我们能把这一块也学会了，我们的爬虫技术就会如虎添翼。

## 运维
这块也是一个重头戏。爬虫和运维也是息息相关。<br>比如写完一个爬虫，怎样去快速部署到 100 台主机上跑起来。<br>比如怎么灵活地监控每个爬虫的运行状态。<br>比如爬虫有处代码改动，如何去快速更新。<br>比如怎样监控一些爬虫的占用内存、消耗的 CPU 状况。<br>比如怎样科学地控制爬虫的定时运行、<br>比如爬虫出现了问题，怎样能及时收到通知，怎样设置科学的报警机制。<br>这里面，部署大家各有各的方法，比如用 Ansible 当然可以。如果用 Scrapy 的话有 Scrapyd，然后配合上一些管理工具也能完成一些监控和定时任务。不过我现在用的更多是还是 Docker + Kubernetes，再加上 DevOps 一套，比如 GitHub Actions、Azure Pipelines、Jenkins 等等，快速实现分发和部署。<br>定时任务大家有的用 crontab，有的用 apscheduler，有的用管理工具，有的用 Kubernetes，我的话用 Kubernetes 就多一些了，定时任务也是很好实现。<br>至于监控的话，也有很多，专门的一些爬虫管理工具自带了一些监控和报警功能。一些云服务也带了一些监控的功能。我用的是 Kubernetes + Prometheus + Grafana，什么 CPU、内存、运行状态，一目了然，报警机制在 Grafana 里面配一下也很方便，支持 Webhook、邮件甚至某钉。<br>数据的存储和监控，用 Kafka、Elasticsearch 个人感觉也挺方便的，我主要用的是后者，然后再和 Grafana 配合起来，数据爬取量、爬取速度等等监控也都一目了然。

## 结语
至此，爬虫的一些涵盖的知识点也就差不多了，怎么样，梳理一下，是不是计算机网络、编程基础、前端开发、后端开发、App 开发与逆向、网络安全、数据库、运维、机器学习都涵盖到了？上面总结的可以算是从爬虫小白到爬虫高手的路径了，里面每个方向其实可研究的点非常多，每个点做精了，都会非常了不起。<br>爬虫往往学着学着，就成为了一名全栈工程师或者全干工程师，因为你可能真的啥都会了。但是没办法啊，都是被爬虫逼的啊，如果不是生活所困，谁愿意一身才华呢？<br>然而有了才华之后呢？摸摸头顶，卧槽，我的头发呢？<br>嗯，大家都懂的。<br>最后最重要的，珍爱生命、珍爱每一根头发。<br>
<br>

> TiToData：专业的短视频、直播数据接口服务平台。
> 更多信息请联系： [TiToData](https://www.titodata.com?from=douyinarticle)
> 覆盖主流平台：抖音，快手，小红书，TikTok，YouTube

