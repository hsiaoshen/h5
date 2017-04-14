＃　URL
1. 浏览器的url请求
2. 循环递归查找DNS服务器
    1. 浏览器缓存 – 浏览器会缓存DNS记录一段时间。 有趣的是，操作系统没有告诉浏览器储存DNS记录的时间，这样不同浏览器会储存个
    自固定的一个时间（2分钟到30分钟不等）。
    2. 系统缓存 – 如果在浏览器缓存里没有找到需要的记录，浏览器会做一个系统调用（windows里是gethostbyname）。这样便可获得系统缓存中的记录。
    3. 路由器缓存 – 接着，前面的查询请求发向路由器，它一般会有自己的DNS缓存。
    4. SP DNS 缓存 – 接下来要check的就是ISP缓存DNS的服务器。在这一般都能找到相应的缓存记录。
    5. 递归搜索 – 你的ISP的DNS服务器从根域名服务器开始进行递归搜索，从.com顶级域名服务器到需要找的域名服务器。一般DNS服务器的缓存中
     会有.com域名服务器中的域名，所以到顶级服务器的匹配过程不是那么必要了。(.com国际顶级域名----.cn国内顶级域名)
 3. 找到相应的目标ip，连接目标ip并于之建立tcp连接。
 4. 向目标web服务器发送http请求
 5. web服务器接受请求后处理
 6. web服务器响应请求返回结果(无效，重定向，正确页面等)
 7. 浏览器接受内容
 
 前段解析
 
 8．开始解析html文件，当然是自上而下，先是头部，后是body
 9．当解析到头部css外部链接时，同步去下载，如果遇到外部js链接也是下载【不过js链接不建议放在头部，因为耽误页面第一展现时间】
 1０．接着解析body部分，边解析边开始生成对应的DOM树，同时等待css文件下载
 11．一旦css文件下载完毕，那么就同步去用已经生成的DOM节点+CSS去生成渲染树
 12．渲染树一旦有结构模型了，接着就会同步去计算渲染树节点的布局位置
 13．一旦计算出来渲染的坐标后，又同步去开始渲染
 14．10-13步进行过程中如果遇到图片则跳过去渲染下面内容，等待图片下载成功后会返回来在渲染原来图片的位置
 15．同14步，如果渲染过程中出现js代码调整DOM树机构的情况，也会再次重新来过，从修改DOM那步开始
 16．最终所有节点和资源都会渲染完成
 
分析结束

 17．渲染完成后开始page的onload事件
 18．整个页面load完成
### ３０１y永久重定向原因

整个过程中会有很多的分别请求，所以TCP连接会很多，并且每一个用完都会自己关了，除非是keep-live类型的可以请求多次才关闭。

中一个原因跟搜索引擎排名有关。你看，如果一个页面有两个地址，就像http://www.igoro.com/ 和http://igoro.com/，搜索引擎会认为它们是两个网站，结果造成每一个的搜索链接都减少从而降低排名。而搜索引擎知道301永久重定向是 什么意思，这样就会把访问带www的和不带www的地址归到同一个网站排名下。

还有一个是用不同的地址会造成缓存友好性变差。当一个页面有好几个名字时，它可能会在缓存里出现好几次。