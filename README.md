# POK&GAStech报告

---
- ##[0. 概述](#0)
- ##[1. 系统流程](#1) 
	- ###[1.1 关键词筛选](#1.1)
	- ###[1.2 关键报社筛选](#1.2)
	- ###[1.3 关键文章筛选分析](#1.3)
	- ###[1.4 Email人物关系分析](#1.4)
	- ###[1.5 问题解答](#1.5)
- ##[2. 分块视图和具体实现](#2) 
	- ###[2.1 初步词频处理](#2.1)
		- [2.1.1 数据处理](#2.1.1)
		- [2.1.2 可视化](#2.1.2)
	- ###[2.2 关键词汇和「关键文章」筛选：词云和可视化搜索引擎](#2.2)
		- [2.2.1 数据处理](#2.2.1)
		- [2.2.2 可视化](#2.2.2)
	- ###[2.3 报社抄袭关系：力学图](#2.3)
		- [2.3.1 数据处理](#2.3.1)
		- [2.3.2 可视化](#2.3.2)
		- [2.3.3 分析结论](#2.3.3)
	- ###[2.4 人物、事件关系：Events Chart](#2.4)
		- [2.4.1 数据处理](#2.4.1)
		- [2.4.2 可视化](#2.4.2)
	- ###[2.5 Email人物关系分析：力学图](#2.5)
		- [2.5.1 数据处理](#2.5.1)
		- [2.5.2 可视化](#2.5.2)
	- ###[2.6 POK发展历程：Story Line](#2.6)
		- [2.6.1 数据处理](#2.6.1)
		- [2.6.2 可视化](#2.6.2)
		- [2.6.3 分析结论](#2.6.3)
	- ###[2.7 初步词频处理事件发生期间的关键节点时间轴：基于TimelineJS的应用](#2.7)
		- [2.7.1 数据处理](#2.7.1)
		- [2.7.2 可视化](#2.7.2)
		- [2.7.3 分析结论](#2.7.3)
- ##[3. 程序清单](#3) 
- ##[4. 分工](#4) 
- ##[5. 文件夹结构说明](#5)
- ##[6. 参考资料](#6)

---
## <h2 id="0"> 0 概述 </h2>

>本可视化系统按照[*VAST Challenge 2014 - Mini Challenge 1*](http://www.vacommunity.org/VAST+Challenge+2014%3A+Mini-Challenge+1)要求完成，基于[Python Django](https://www.djangoproject.com/)框架构建的网站，以及主要由[d3.js](http://d3js.org)实现的各数据可视化视图。除此之外，用到的主要外部库或框架技术包括：
 
> - [JQuery Bootstrap](https://github.com/jquery-ui-bootstrap/jquery-ui-bootstrap)控件: 网站主页顶部导航栏

> - [TimelineJS](http://timeline.knightlab.com/): 兴趣日期的事件时间线

> - [在线词云生成引擎](http://www.jasondavies.com/wordcloud/#): 词频分析阶段

## <h2 id="1"> 1 系统流程 </h2>
###  <h3 id="1.1"> 1.1 关键词筛选 </h3>

>此部分旨在通过词频分析摘取出重要词汇，用于筛选文章。选择如下的方式对词频进行可视化：

>1. **文章词频的可视化：词云**
  	- 使用stopwords过滤脚本过滤掉多余的词
  	- 使用在线词云生成器生成词云
    - 制作了全部文章和2014年文章的词云

>1. **文章中高频词语随时间出现次数的纵向比较：彩点图**

### <h3 id="1.2"> 1.2 关键报社筛选 </h3>

>此部分报社此部分旨在通过可视化的方法寻找出创造性最高的一些报社。然后，从上一部分筛选完毕的文章中，进一步筛选，只留下重要报社的文章。

>简单地浏览一些报道文章，很明显地可以看出有不少重复报道，甚至报社之间还存在抄袭关系。那么很多基本只抄袭的报社的报道我们就可以忽略，仅考虑少部分原创性极高的报社。同时，报社每年占所有报社的发文比率也是一项值得参考的数据，也将其考虑进去。

>1. **报社的内容独创性分析：力学图（包含内容相似关系）**
   - 一个表示报社之间抄袭关系的力学图，边表示相互抄袭的关系，边越粗相互抄袭的次数越多
   - 节点代表报社，节点的面积表示原创值*(被抄袭次数+0.1 / 抄袭次数+0.1)*的比值，原创值越大面积越大
   - 鼠标经过某节点的时候会高亮相应的边
   - 找到面积较大的节点，这些节点就是可信度较高的报社，并进一步可以分析出报社之间的派别、倾向
   - 找到「Homeland Illumination」和「Kronos Stars」这两家关键报社，它们的报道独创性较高，且观点经常冲突

>1. **报道数量时间变化趋势的分析（根据报社分类）：栈式区域图**
   - 通过分析所有的文章，统计所有报社的发文数量
   - 使用栈式区域图，按时间绘制发文比例的趋势图
   - 横轴时间（年份）
   - 不同的颜色代表不同的报社
   - 纵轴代表各个报社的发文比例
   - 通常认为发文比例大报社的具有较高研究价值

###  <h3 id="1.3">  1.3 关键文章筛选分析  </h3>

>此部分旨在进一步从剩下的文章中找出关键文章、重要时间，并建立起人物与事件发展的关系。重要人物的名字事先通过阅读5、10年的记录文档中获取出。

>1. **文章内容的深入分析：搜索引擎 & 人工阅读摘要**
   - 通过以上两点筛选出几家关键的报社
   - 计算文章中的所有词语（除了stopwords）的「TF-IDF」属性并建立倒排索引，架设简单的基于TF-IDF算法排序搜索引擎
   - 通过这个简单的搜索引擎，搜索词云中的高频词和关键报社，得到一部分文章最相关文章的智能摘要，加上两篇阶段性报道，称为「关键文章」
   - 人工阅读分析这些「关键文章」，大致理清了整个事件的来龙去脉

>1. **POK的发展历程时间线：Events Chart**
   - 根据分析「关键文章」的结果，得到一些关键人物
   - 通过自动筛选，在「关键文章」中得到与人物相关的描述文章
   - 根据这些文章的事件顺序，整理出POK发展历程的大致时间线
   - 视图包含多条时间线，每条时间线表示某个特定的人物参与的事件的时间线
   - 视图上每个点代表一个事件，鼠标移动到事件上是会出现该事件的摘要信息

### <h3 id="1.4"> 1.4 Email人物关系分析 </h3>

>此部分旨在从邮件来往力学图中找出人物的联系关系

>1. **Email-header数据和staff数据：力学图**
   - 一个表示邮件收发关系的力学图，边表示邮件的收发关系，收发次数多的边比较粗
   - 节点表示人物，节点按照某项属性进行分组，同一组的节点颜色相同
   - 可以在页面上方选择分组的关键属性，改变关键属性会导致图的重绘
   - 鼠标经过节点的时候会浮动显示该人物的基本信息，同时高亮该人物的邮件收发的边

### <h3 id="1.5"> 1.5 问题解答 </h3>

>1. **POK发展历程：Story Line**
   - 将总结出来的人物、事件关系通过故事线的方式呈现
   - 根据事件将事件分布在横向方向上，由于一年内有多件事情，将多见事情分布在纵向上；事件由灰色的点来表示，点的灰色程度表明参与其中的人数的多少
   - 人物按照POK（蓝色）、边缘人物（黑色）、GAStech（红色）进行分类
   - 每个人物经历的事情，按照时间顺序串联起来
   - 移动到一个人的某条线上，将高亮出整个人的故事线
   - 移动到一个时间上，将高亮出与其相连的线，并显示出故事内容

>1. **POK的人事架构图：拓扑图**
   - 通过分析「关键文章」内容以及大事记的内容，得出人事架构变迁的关系
   - 绘制POK发展过程中重要人事架构变化的分析图

>1. **会议期间的时间线：TimelineJS**
   - 分析会议期间的「关键文章」内容，整理内容中出现的时间信息
   - 通过得到的时间信息进行排序，整理出会议发展顺序的时间线
   - 将这个时间线使用TimelineJS进行可视化，得到时间轴视图
   - 时间轴上的字母表示报社，H表示「Homeland Illumination」，K表示「Kronos Stars」，这是两家关键报社

>1. **推测结论**
   - POK绑架论：POK绑架了GAStech的职员，通过此方式来实现他们多年来未能实现的夙愿
       - 根据21日Homeland Illumination发表的一封信件
	   - 从POK与GAStech多年以来的冲突推测
   - GAStech内部策划：GAStech内部的重要人员策划了一次假绑架，并想借此嫁祸于POK来消除其势力
	   - 根据20日报道中GAStech有职员称目击领导携款逃离
	   - 根据20日报道中有人目击了GAStech大楼屋顶的直升机

---

## <h2 id="2"> 2 数据处理、可视化实现、分析结论 </h2>
###  <h3 id="2.1"> 2.1 初步词频处理 </h3>
#### <h4 id="2.1.1">  2.1.1 数据处理 </h4>
1. 从职员名单中提取出所有GAStech 职员的名单，以职员名单为关键词使用python 脚本从所有文章中筛选出包含有GAStech 职员名字、GAStech 和POK 关键字的文章，将它们分划到不同的目录中，处理出每一个目录中所有报道的词频信息，筛选出可以描述关键事件的词；
1. 编写python脚本分析所有的报道文本，识别出对应的发文时间和报社信息并转化为统一的格式，存入中间文件`articleTime.csv`以备后续数据预处理过程方便使用
1. 编写python脚本从中间文件中读取数据，将报道按日期切分复制到`ByDay/`中的各个子文件夹，并分别统计各个有报道文章出现的日期的文章数量，以`tsv day-month-year	number`格式存储
1. 编写python脚本从中间文件中读取数据，将报道按年份切分复制到`ByYear/`中的各个子文件夹，并分别统计各年度几个主要报社的文章在它们的文章总数中所占比例，以`year	The-Abila-Post	News-Online-Today	Kronos-Star	Homeland-Illumination	Centrum-Sentinel`格式存储，其中每项数字都是以0-100的浮点数表示的比例值。

#### <h4 id="2.1.2"> 2.1.2 可视化 </h4>
  - 一个表示总的报道数量随时间推延的变化趋势的区域式折线图，横轴表示日期，纵轴蓝色部分高度表示当日的文章数量；能够清晰地分辨出文章高度集中在14年往后的情况，因此对近期文章进行基于时间序列关系的分析并非十分有效
  - 一个表示几个主要报社（发文总数量占优的五家）每年度报道数量占它们总和比例的堆栈式区域图，每种颜色代表一个报社，同种颜色在同一横轴座标上对应的纵轴高度表示相对的报道数量比例；能够看出哪些报社随时间的延续性较强，历史底蕴较为深厚，或是哪些报社在短期内有爆发式的发文趋势


### <h3 id="2.2"> 2.2 关键词汇和「关键文章」筛选：词云和可视化搜索引擎 </h3>
####  <h4 id="2.2.1"> 2.2.1 数据处理 </h4>
1. articles文件夹中所有的845篇报道，包含时间、报社信息（绝大多数）和正文内容
1. 利用`Vector Space Model`对比每两篇文章的相似度，具体为：
 	- 以两篇文章中出现过的词的并集作为用于比较的向量的维度，统计它们的词频，并计算两个向量经过归一化处理得到的内积
 	- 若内积的平方超过`0.7`的阈值，那么视时间较晚的一篇为有抄袭嫌疑的重复信息；若时间为同一天，那么认为较短的为重复信息
 	- 被判断有抄袭嫌疑的文章号被标记为待删除，为了加快筛选效率接下来不对这个待删除列表中的文章作对比而直接跳过
 	- 全部对比结束后，一次性删除全部被标记为抄袭嫌疑的文章（超过300篇）
 - 创建一个stopwords（意义不大的常见词汇）表
 - 对去重后的articles中各篇文章进行解析、分词后为除去stopwords中的词以外的所有词汇分别建立倒排索引，每个索引文件每行的格式为`出现该词的文章序号:出现次数：出现行数1:出现行数2:...:最后一个出现行数`
 
####  <h4 id="2.2.2"> 2.2.2 可视化 </h4>
 - 编写脚本将全部文章和2014年的文章分别整理、精简到Summing.txt和Summing2014.txt中并利用在线词云生成器获得可视化结果，从中可获取全局和事件发生时间段分别的高频词汇，以备作进一步的分析
 - 架设了一个搜索引擎的前端
 	- 基础版本为文字显示搜索结果，支持多关键字交集搜索；对于每一个关键字找到索引，计算出现过的文章与其对应的TF-IDF值，并以之为根据作相关度排序，对于每项结果显示文章相关段落的快照，方便初步了解事件内容、提取关键信息
 	- 可视化版本为词云显示搜索结果，将搜索出含该关键字的文章整理出来，按stopwords表精简、合并后存放为一个服务器端静态文件地址下的临时文件，并将搜索结果页面转链到一个在线词云生成器，将临时文件地址作为参数交给它，以获得效果较好的搜索结果对应的词云


###  <h3 id="2.3"> 2.3 报社抄袭关系：力学图 </h3>
####  <h4 id="2.3.1"> 2.3.1 数据处理 </h4>
1. 对于每一家媒体，统计该媒体的文章在所有文章中出现的次数，并根据所有媒体的发文活跃度绘制媒体活跃度随年份变化的stack chart；
1. 对于不同的媒体，使用查重算法（阈值为0.7）找出它们之间存在的抄袭关系。以发表时间靠前为原创者，一家报社发表的被抄袭次数为N，抄袭次数为M，则原创程度可以近似于`(N+0.1)/(M+0.1)`，加0.1为了避免除零出错，也避免过大的数值偏移。根据所有报社的抄袭率数据绘制力学图。同时，力学图还能够以边明暗变化的方式反映媒体之间的抄袭关系，如果两个节点之间连有一条边，表示媒体之间存在抄袭关系。同样地，抄袭次数越多边越粗。
1. 根据以上两个可视化结果，可以很明显地定位到我们要找的重要媒体。

####  <h4 id="2.3.2"> 2.3.2 可视化 </h4>
- 每个点代表一个报社，点的大小代表报社的原创系数大小
- 点之间的连线代表抄袭或者被抄袭的关系，线的粗细代表抄袭、被抄袭的次数
- 移动到一个点上，可以高亮与它相关联的报社

####  <h4 id="2.3.3"> 2.3.3 分析结论 </h4>
- 从中可以看出几个重要报社，`Kronos Star`、 `Homeland Illumination`等
- 根据此结果，筛选剩余文章，只查看重要报社
- 通过人工阅读部分文章，发现其中Kronos Star与Homeland Illumination有明显的立场倾向，Kronos Star倾向于GASTech与政府一方，而Homeland Illumination倾向于POK一方


### <h3 id="2.4">  2.4 人物、事件关系：Events Chart  </h3>
####  <h4 id="2.4.1"> 2.4.1 数据处理 </h4>
1. 利用events.json：
 
		[
				{
  		  			“name”:”...”,
  		  			“events”:[{“date”:”...”, “event”:”..”},...]
  				}, ...
		]	
	- name为人物名字
	- events数组为人物经历过的事件
	
####  <h4 id="2.4.2"> 2.4.2 可视化 </h4>
- 根据分析「关键文章」的结果，得到一些关键人物
- 通过自动筛选，在「关键文章」中得到与人物相关的描述文章
- 根据这些文章的事件顺序，整理出POK发展历程的大致时间线
- 视图包含多条时间线，每条时间线表示某个特定的人物参与的事件的时间线
- 视图上每个点代表一个事件，鼠标移动到事件上是会出现该事件的摘要信息


### <h3 id="2.5"> 2.5 Email人物关系分析：力学图 </h3>
####  <h4 id="2.5.1"> 2.5.1 数据处理 </h4>
1. 所有email头的信息Email header.csv的数据结构如下：

		From, to, Date, Subject

	- From属性仅包含一个发件人的email地址
	- To属性包含复数的收件人email地址
	- Date属性为发件日期，包括从2014/1/6~2014/1/17日的邮件
	- Subject属性为邮件的标题，其中以”RE:”开头的邮件为回复的邮件
1. 所有职员的简历，xlsx表文件，包含每个人的姓名、性别、出生日期、出生地点、国籍、居住地、任职信息等
1. 使用python读入邮件头信息，根据发送 - 接收关系整理出人与人之间的关系，统计出两两之间的收发次数，用以下结构存储。

		link { source: 发送者邮箱 target: 接受者邮箱 time: 收发次数 }
1. 使用python读入xlsx表文件，按照不同属性将职员进行分类，保存成json格式

####  <h4 id="2.5.2"> 2.5.2 可视化 </h4>
  - 一个表示邮件收发关系的力学图，边表示邮件的收发关系，收发次数多的边比较粗
  - 节点表示人物，节点按照某项属性进行分组，同一组的节点颜色相同
  - 可以在页面上方选择分组的关键属性，改变关键属性会导致图的重绘
  - 鼠标经过节点的时候会浮动显示该人物的基本信息，同时高亮该人物的邮件收发的边


###  <h3 id="2.6"> 2.6 POK发展历程：Story Line </h3>
#### <h4 id="2.6.1"> 2.6.1 数据处理 </h4>
1. 人工阅读5年、10年的总结，与筛选出来的文章。摘出重要人物(name.txt)，摘取重要事件并结合之前得到的重要事件(Timeline.txt)。通过程序处理(handler.cpp)将人物、事件之间建立起联系；得到events.json，其结构如下：
	
		[
  				{
  		  			“name”:”...”,
  		  			“events”:[{“date”:”...”, “event”:”..”},...]
  				}, ...
		]

####  <h4 id="2.6.2"> 2.6.2 可视化 </h4>
- 该故事线视图为自己画点、线实现的layout；美观程度上还有待改进，但是包括了人物、事件之间联系的必要信息
- 根据事件将事件分布在横向方向上，由于一年内有多件事情，将多见事情分布在纵向上；事件由灰色的点来表示，点的灰色程度表明参与其中的人数的多少
- 人物按照POK（蓝色）、边缘人物（黑色）、GAStech（红色）进行分类
- 每个人物经历的事情，按照时间顺序串联起来
- 移动到一个人的某条线上，将高亮出整个人的故事线
- 移动到一个时间上，将高亮出与其相连的线，并显示出故事内容

#### <h4 id="2.6.3"> 2.6.3 分析结论 </h4>
- 明确被提及是GASTech员工的Isia Vann，曾经被作为POK的成员逮捕。


###  <h3 id="2.7"> 2.7 事件发生期间的关键节点时间轴：基于TimelineJS的应用 </h3>
####  <h4 id="2.7.1"> 2.7.1 数据处理 </h4>
1. 通过分析经过筛选得到的会议期间的「关键文章」，并且按照时间排序、分别归纳后得到的一系列关键信息的整合`NewsCombined.txt`

1. 编写python脚本将`NewsCombined.txt`处理为TimelineJS基础样式所能够识别的序列化的json格式（见`djcode/mysite/static/timeline_js/data.json`）

####  <h4 id="2.7.2"> 2.7.2 可视化 </h4>
 - 利用TimelineJS的Github项目主页上提供的嵌入代码和样式表文件，在服务器上架设了一个简单的时间线应用
 - 支持在下方时间轴上拖动、点击查看任意时间点的关键节点事件，也支持在上方的主显示区域左右按动，按顺序浏览

#### <h4 id="2.7.3"> 2.7.3 分析结论 </h4>
- 根据兴趣日期的时间轴，我们可以很清楚地梳理出事发两天发生的事情，对整体情况能够的出一个简单的推论。

##<h2 id="4"> 3 程序清单 </h2>
| 文件夹 	|      文件名       	|                                 用途                                	|
|:-----:|:------------------:|-----------------------------------------------------------------------|
|tools	| csv2json.py     	| 将csv文件转化为json格式                                               	|
|     	| xlsx2json.py     	| 将xlsx表格转化为json格式                                             	|                                         			|
|     	| jsonsplit.py     	| 将邮件信息文件和职员简历文件整理成一个统一的「节点 - 边」信息，按照属性分类      	|
|		| email.json		| 转化后的邮件头信息                                                 		|
|       | Employee Records.json | 转化后的人员信息文件                                               	|
|		|Main.java, CSVKeeper.java| 预处理email header.csv以及部分EmployeeRecords的信息					|
|events	| name.txt			| 摘选出的重要人物名字														|	
|		| Timeline.txt		| 摘选出的重要事件与其时间													|	
|		| handler.cpp		| 预处理摘选出来的人物、事件,生成关联的events.json文件							|
|       | events.json		| 摘选出的人物、事件联系的json文件                                    		|
|		| content.json     	| 整合后的「节点 - 边」信息文件                                       		|
|articles| divideByDay.py	| 将报道文章按日期分开的脚本                                         		|
|		| divideByYear.py	| 将报道文章按年份分开的脚本                                       			|
|		| divideByYear-pok.py| 将含“pok”的报道文章按年份分开的脚本                             			|
|		| indexer.py  		| 读取articles/建各term的倒排索引的脚本                						|
|		| tsvGenerator.py  	| 生成区域式折线图和堆栈式区域图所需的数据文件的脚本                          	|
|		| who_copies_who.py	| 统计报社互相抄袭关系系数，生成抄袭关系和报社观点集群关系力学图所需的数据文件的脚本 	|
|		| copied_times.txt 	| who_copies_who.py 预览输出并排序的结果                      				|
|		| copy.json  		| 抄袭关系和报社观点集群关系力学图所需的数据文件                     			|
|		| articleTime.csv  	| 每篇报道的[编号，时间，报社名]，用于减小许多后续处理脚本的工作量 				|
|		| pre.py          	| 包含统计出paperStat.txt、articleTime.csv的两个功能模块                    	|
|		| paperStat.txt    	| 各报社发文总数的统计                                                		|
|		| data.json        	| 区域式折线图所需的数据文件                                             	|
|		| muchdata.json    	| 堆栈式区域图所需的数据文件                                          		| 
|		| trimming.py		| 报道文章去重的脚本|
|		| parseArticles.py		| 根据关键字作文章内容摘要预处理脚本|

## <h2 id="4"> 4 分工 </h2>
|	姓名		|	学号		|	工作		|
|:---------:|:----------:|-----------|
|董可扬		|3130000020	|架设索引、搜索引擎、词云|
|			|			|完成词频、报道量有关视图|
|			|			|运用TimelineJS实现事件时间线视图|
|			|			|报道文章数据预处理的Python脚本|
|			|			|整体网站的架设和Django框架的运用|
|			|			|文章查重和计算报社抄袭关系算法的实现
|王儒		|3120102084	|Employee Records.csv处理，email完成力学图|
|			|			|利用处理好的数据完成基于人物驱动的时间事件视图，即Events Chart|
|			|			|数据格式转换的Python脚本|
|			|			|可视化系统整体思路和流程梳理|
|柴宇进		|3120000011	|Email.csv处理，辅助完成email力学图|
|			|			|阅读筛选出的文章，摘选5、10年重要信息，以及重要信息预处理程序|
|			|			|利用处理好的数据，完成报社抄袭关系力学图|
|			|			|完成POK发展历程的StoryLine|
|			|			|文档整合|
|安磊		|3120000058	|报社抄袭关系的预处理|
|			|			|完成文章预处理脚本|
|			|			|阅读筛选出的文章摘要|
|			|			|按时间整理20、21日的事件|
|			|			|POK人事架构图、答案总结|


## <h2 id="5"> 5 文件夹结构说明 </h2>

>`Scripts and Data/`: 用于数据预处理、格式转换等的Python、Java或C++脚本； 数据处理的中间结果文件，或支持各数据视图的\*.json、\*.tsv、\*.csv文档；

>`djcode/`: 作为整体系统展示平台的网站根目录；

>`djcode/mysite/app/`: 包含各信息可视化视图的\*.html文件和Javascript脚本；

>`djcode/mysite/static/`: 包含网页数据呈现所需要的各静态文件，即支持视图的序列化数据、图片、CSS样式表、JS库、经过去重的源报道文档、倒排索引（数量较多，已经打包）、查询时生成的必要临时存储文件等。

## <h2 id="6"> 6 参考资料 </h2>

>[Data-Driven Documents Gallery](https://github.com/mbostock/d3/wiki/Gallery): https://github.com/mbostock/d3/wiki/Gallery

>[Data-Driven Documents Wiki](https://github.com/mbostock/d3/wiki): https://github.com/mbostock/d3/wiki

>[The Django Book](http://www.djangobook.com/en/2.0/index.html): http://www.djangobook.com/en/2.0/index.html

>[Timeline JS](https://github.com/NUKnightLab/TimelineJS):https://github.com/NUKnightLab/TimelineJS