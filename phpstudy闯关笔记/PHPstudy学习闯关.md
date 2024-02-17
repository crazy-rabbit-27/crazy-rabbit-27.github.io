## PHPstudy闯关学习笔记

通过xss了解到的知识点：（自行回顾）

- 跨网站脚本（Cross-site scripting，XSS） 又称为跨站脚本攻击，是一种经常出现在Web应用程序的安全漏洞攻击，也是代码注入的一种。

  XSS是由于Web应用程序对用户的输入过滤不足而产生的，攻击者利用网站漏洞把恶意的脚本代码注入到网页之中，当其他用户浏览这些网页时，就会执行其中的恶意代码，对受害者用户可能采取Cookie窃取、会话劫持、钓鱼欺骗等各种攻击。这类攻击通常包含了HTML以及用户端脚本语言。

- URL的全称是“Uniform Resource Locator”，中文意为“统一资源定位符”。

  URL是通过互联网来定位和访问特定资源的地址，常见于网页浏览和HTTP请求中。

- DIV是层叠样式表中的定位技术，全称DIVision，即为划分。

  有时可以称其为图层。DIV元素是用来为HTML（标准通用标记语言下的一个应用）文档内大块（block-level）的内容提供结构和背景的元素。

- 漏洞存在的主要原因为：

  参数输入未经过安全过滤

  恶意脚本被输出到网页

  用户的浏览器执行了恶意脚本

- php中$符号加上字符串，就是一个变量源名或对象名。

- 最主要的区别是GET方式的网址可以看到参数，而POST方式URL始终不变。

- 数据流是指在计算机系统中，数据在各个组件（如程序、模块、子系统等）之间传输和处理的过程。

  在数据流中，数据经过一系列处理后，输出到下一个组件或者最终输出到终端用户。[学习来源](https://cloud.tencent.com/developer/techpedia/1734)

- alert()的功能是用于显示带有一条指定消息和一个OK按钮的警告框。

- ......



level1：

根据源代码发现更改name=test中test可以改变页面的内容，如图![test变为test1](C:\Users\Administrator\Pictures\Camera Roll\欢迎来到level1.png)

对name参数进行payload，将name=< script>alert(’输入什么都行‘)</ script>也就是用JS脚本代码输入，用于在网页中弹出一个警告框。（ 我感觉第一关考的应该是get提交、反射型，然后在name后面写恶意代码来攻击 )

（payload，翻译过来是有效载荷

通常在传输数据时，为了使数据传输更可靠，要把原始数据分批传输，并且在每一批数据的头和尾都加上一定的辅助信息，比如数据量的大小、校验位等，这样就相当于给已经分批的原始数据加一些外套，这些外套起标示作用，使得原始数据不易丢失，一批数据加上“外套”就形成了传输通道的基本传输单元，叫做数据帧或数据包，而其中的原始数据就是payload）

level2：

在输入框或者keyword中输入的内容都会改变搜索的的文字，如图：![2](C:\Users\Administrator\Pictures\Camera Roll\2.png)

尝试以第一关的方式输入< script>alert()</ script>，但是没有弹窗。通过对比level1和level2的源代码对比可以发现，是包含在value中，所以我觉得就是要把前面的内容和输入的隔开，所以我输入了"">< script>alert()</ script>,目的就是用>将两者隔开。

上网了解后发现除了上述方法外，还有多种方法：

- ">< script>alert()< /script>//

- " onclick="javascript:alert()      

  闭合value属性，然后在input标签内加入事件属性："onclick="alert(1)>   注释：alert（）前面是指所用的脚本语言

level3：

通过观察源代码，发现输入内容< input name=keyword  value='输入内容在此！！！'> 被包含在value=‘’的引号之中。输入"">< script>alert()</ script>发现都被实体化。并且出现了&It和&gt，查阅资料：![img](https://img-blog.csdnimg.cn/2741e08aec6d4ae79f2889f5448ed5e6.png)

双引号不能用来闭合了，但是单引号没有过滤，所以我们可以使用单引号来闭合标签。但是一般来说单引号也会被函数过滤，原因是.htmlspecialchars函数中的第二个参数默认值仅编码双引号，而本关代码只使用了一个参数，所以导致可以使用单引号对标签进行闭合。查阅资料：<img src="https://img-blog.csdnimg.cn/5441715e7db441d2a047df82fee8ad6b.png" alt="img" style="zoom: 80%;" />

所以，输入' onclick='alert(内容什么都可以)'

level4：

对比level3的源代码可以发现value=从单引号变成双引号，并且输入< script>alert(1)< /script>可以发现尖括号被过滤。输入' onclick='alert(内容什么都可以)'也不行，所以尝试将输入的内容也改为双引号"onclick="alert(1)"，成功。

查看源代码发现$str2=str_replace(">","",$str);$str3=str_replace("<","",$str2);用替换函数将尖括号直接替换为空。查阅资料：[关于JS单双引号的区别](https://blog.csdn.net/qq_37221396/article/details/80513681)

level5：

- 输入" onclick="alert(1)"，发现出现    "o_nclick="alert(1)"   情况；

- 输入< script>alert(1)</ script>，发现出现<scr_ipt>alert(1)</ script>  情况；

从上述两种情况发现都出现了“_” 符号，原因：根据level4相同道理，查源代码 $str2=str_replace("<script","<scr_ipt",$str);

$str3=str_replace("on","o_n",$str2);  所以只能尝试不带< script>和on的标签 。[另寻办法](https://www.w3school.com.cn/tags/att_a_href.asp)也就是单字符标签< a>类似一个超链接。

输入："><a href=javascript:alert('内容随意') > hack< /a>

level6：
输入相同level5的内容，发现情况相同，应该是在level5的源代码基础上加了一些内容，查看源代码:

$str2=str_replace("<script","<scr_ipt",$str);

$str3=str_replace("on","o_n",$str2);

$str4=str_replace("src","sr_c",$str3);

$str5=str_replace("data","da_ta",$str4);

$str6=str_replace("href","hr_ef",$str5);

可知以上输入方式均会被实体化，并且value=" '.$str6.' "是有单双引号包含的变量。被过滤的部分较多，但是没有过滤大小写的转换。所以我们可以输入：

"> < Script>alert("内容随意")< / script>

level7：

重复前几关操作，发现输入的内容会变为空格；查看源代码发现关键字都被替换成空格，其实就是在原来的script之中的任意一个地方直接加入一个script，注意这个加入的script不能分开，识别到后这个加入的script会被替换成空值，剩下的部分自动拼接，执行时空格会被自动忽略。通俗一点来说，我觉得就是要找两个script，一个顶包的script让它被识别为空格来保护另一个script。

输入：">< sscriptcript>alert('内容随意')< /sscriptcript>

level8：

第八关通过输入之前的内容和查看源代码可以发现不仅过滤了许多内容，而且发现了一个新函数htmlspecialchars($str).[关于此函数的了解处](https://www.w3school.com.cn/php/func_string_htmlspecialchars.asp)。因为下面有超链接，现在只需要绕过JavaScript的关键字黑名单，对JavaScript中的某一个或某一部分的编码用html实体形式的ascii码即可，加入仅仅转换r，则大小写形式的html实体都可以，也就是R（R）和r（r）。

上网了解：HTML实体由三部分组成，“&#+ASCII+;”

所以输入：javasc&#82;ipt:alert(内容随意)

level9：

查看源代码可以发现与上一关的最大不同点就是

if(false===strpos($str7,'http://')){

 echo '< center>< BR>< a href="您的链接不合法？有没有！">友情链接< /a>< /center>';

​    }else

......

判断字符"http://"是否在字符串中出现，如果没出现就判定非法链接，返回false；所以说就是就是需要一个在前一关加上判断字符，再将它注释掉就可以啦！所以我们输入：

javasc&#82;ipt:alert(内容随意)//http://

level10：

第十关出来的时候感觉和第一关还挺像，但是打开源代码发现完全不一样[笑哭]。查看中发现"<" ">"被过滤掉，还有出现了一个单词hidden"隐藏"，[查阅资料处](https://blog.csdn.net/Randy_Shenyp/article/details/73436870)，可以发现有一个< input>标签内有一个.$str33.来控制前面的内容，所以在t_sort中将隐藏变为可视。简单来说，我的思路就是将第十关变为第一关，将第十关的输入框给显示出来，则在keyword=的后面输入1&t_sort=" type="text" ；然后画面出现输入框，在输入框内输入：1&t_sort=" type="text"  οnclick="alert('xss')   ，因为尖括号被过滤所以输入内容和第一关还是有些不同，可是行不通，输入后发现?keyword消失，所以我将?keyword一同输入,所以输入：
?keyword=nul&t_sort=" type="text" onclick="alert('内容随意')

level11：

查看源代码多了一个t_ref，剩下的和上一关差不多，所以我尝试了上一关的方法，发现t_sort能够传参，但是无法通关。与上一关最大的差别就是出现了$str11=$_SERVER['HTTP_REFERER'];   查阅关于[参数referer的相关资料](https://blog.csdn.net/u011250882/article/details/49679535)，改变keyword的内容发现并不会改变value=''内的内容。

找到攻略，发现需要抓包工具Burpsuite，通过抓包发现没有referer这个数据开头的，所以我加上了referer：内容随意，来看看会出现什么情况，然后再修改referer处的数据为：

referer:"type="text" οnclick="alert('内容随意')

level12：

直接查看源代码，发现出现陌生的语句$str11=$_SERVER['HTTP_USER_AGENT'];[查阅资料](https://blog.csdn.net/Yuppie001/article/details/135132582?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-2-135132582-blog-98475553.235%5Ev43%5Epc_blog_bottom_relevance_base3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-2-135132582-blog-98475553.235%5Ev43%5Epc_blog_bottom_relevance_base3&utm_relevant_index=3)并对比上一关，发现上一题中的t_ref被修改成了t_ua，在这t_ua标签中value属性的值比较熟悉，使用burp抓包查看，发现为user-agent中的值，添加上User-Agent: "type="text" οnclick="alert('内容随意')即可，类似level11。

level13：

此关类似前几关，t_ua被替换成了t_cook，发现t_cook标签中value的值正好为cookie，所以也就是加上语段Cookie: user="type="text" οnclick="alert('内容随意')即可。

level14：

出现三个网站，分别进入网站后发现是关于图片AI的网页（软件），查看此关脚本发现极其简短，我没找到什么突破口，[找寻14关攻略](https://blog.csdn.net/2301_77485708/article/details/132097198?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-132097198-blog-127931237.235%5Ev43%5Epc_blog_bottom_relevance_base3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-132097198-blog-127931237.235%5Ev43%5Epc_blog_bottom_relevance_base3&utm_relevant_index=2)（得知此题涉及exif xss漏洞，了解到当存在 Exif XSS 漏洞时，攻击者可以在图像的 Exif 数据中插入恶意的 JavaScript 代码，所以要修改iframe标签内内容，将src=http://127.0.0.1，再修改图像数据< script>alert(内容随意)< /script> ）

level15：

改变keyword=后面的内容发现没有什么变化，查看源代码发现两段陌生语段：

$str = $_GET["src"];

echo '< body>< span class="ng-include:'.htmlspecialchars($str).'">< /span>< /body>';

[ng-include指令作用](https://www.runoob.com/angularjs/ng-ng-include.html)了解到ng-include,加载外部html，script标签中的内容不执行、加载外部html中含有style标签样式可以识别并且如果单纯指定地址，必须要加引号。查看发现包含的是一个页面，就尝试包含第一关的页面，

构造的语句为：

level15.php?src='level1.php?name=<img src=x onerror=alert(/XSS/)>'

level16：

出现陌生&nbsp，查询了解到是"空格"的意思，所以空格、反斜杠、script均被替换为空格，那么就要将被替换的部分用一个没有script和/来输入，通过查询资料得知可以使用回车的url编码可以绕过。用链接的形式过关，即可输入：

< a%0Ahref='javas%0Acript:alert("xss")'>xss

level17：

出现的链接"成功后，点我后进入下一关"，我点击后就直接进入level18了！！！？？？

level18：

页面没看出什么，直接查看源代码，出现陌生标签< embed>，[查询关于此标签的资料](https://www.w3school.com.cn/tags/tag_embed.asp)，那么就应该是用来嵌入图片的，然后可以看到是arg01和arg02两个变量对特殊字符进行了过滤。通过攻略了解到可以考虑用onclick或onmouseover事件绕过。查询[onmouseover作用](https://www.so.com/link?m=zSNdT%2B9%2FK4TiER2OWe%2F78e%2BurxWQYQRAMBxKfPoc7Q1zhg0GPePxRIl7Z5rx2inbjbmzGtMs70nAWW1xTB93QjinbwKrOcTU6AH9%2B%2BLwg3i73SeOfLQlACRyZmxp1MA4CIKFrrv%2B4exk73iNCqoLICLOBcT2%2BzJMwSNtUYv%2FF3JwEb%2FYK)。arg01和arg02因为这两个变量是互相拼接起来的，所以在输入arg02时在b之后加一个空格，当浏览器解析到b的时候就停止判断，然后将onmouseover看作另外一个属性,所以在url处的后面输入：
[空格]onmouseover=alert(内容随意)

level19：

捣鼓半天没什么进展，查看源代码有段陌生的语段<embed src="xsf03.swf?'......，[查询相关资料](https://www.w3school.com.cn/html5/att_embed_src.asp)看到这段资料我就意识到这关对我应该。。。[笑哭]；src=<u>"xsf03.swf?'</u>单独查看这个链接，可以看到个图片：![img](https://img-blog.csdnimg.cn/20191123143616583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9iYXluay5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)

flash里面提示sifr.js是没有定义的，所以这不仅仅是个图片。查看攻略需要对`flash`进行[反编译](https://so.csdn.net/so/search?q=反编译&spm=1001.2101.3001.7020)查看源码，使用的是[jpexs](https://pan.baidu.com/s/1qdavej11px300S4vCy7RkQ)，[攻略地点](https://blog.csdn.net/u014029795/article/details/103213877)；涉及到反编译，大概了解了些，但是感觉我功力不深，记录下payload：

arg01=version&arg02=< a href="javascript:alert(1)">123< /a>

level20：

因为网页一片空白，所以我直接就查看源代码，感觉和level19差不多，最大的不同就是这段：

payload:
?arg01=id&arg02=\%22))}catch(e){}if(!self.a)self.a=!alert(1)//&width&height

所以，我猜应该考点也是flash xss。

但是，上网了解有一种万能的方法触发弹窗，就是编辑HTML手动添加onmouseover时件。![img](https://img-blog.csdnimg.cn/20200525001513248.png)

