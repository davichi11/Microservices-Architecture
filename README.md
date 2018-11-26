---
description: 工作忙加平时懒，拖了出版社一年有余仍未完工，遂放弃出版，转开源电子书，有空时心情好时可以自由地补充完善。
---

# 微服务架构设计

### 成书原因

架构的重要性已不言而喻，但凡一个软件项目必定会涉及服务的架构，因此市面上介绍此类知识的书籍可谓汗牛充栋。机缘巧合下笔者结识一出版社朋友并希望我写一本微服务相关的书籍，笔者虽在此方面小有经验，但颇为担心落得个拾人牙慧的骂名。 在几经调研后，笔者发现微服务架构的话题很大，可切入的方式很多，结合自身经验还是有一些差异化的内容可写，遂开始尝试性的撰写此书。一般人偏理解性文章的平均阅读速度是200-250字/分钟，本书撰写之初就定下15万字的上限，即希望大部分符合要求的读者（见本书受众）都可以在1天内（12小时左右）看完，能够理解本书所写的内容进而了解到微服务架构的全貌、关键点并有助于工作提升，在未来的一段时间内如有余力可继续完成本书的扩展阅读以进一步加深理解。 由于工作强度、书籍质量及个人能力等原因此书的完成前后经历了近一年时间，在此特别感谢家人的理解及同事的帮助及指点。 

### 本书特点

本书不会重复造轮子，写出差异化，以实用性为初心，实例化引导为手段，避免阳春白雪式的理论宣导，或是皮相之谈的代码解读。总结而言本书的特点如下：

**实例导向，理论为辅**：笔者多年服务于互联网金融公司，本书以信贷系统这一真实项目为线索，循序渐进地介绍服务架构的来龙去脉，微服务架构的基础性理念介绍是必须的，但不会生搬硬套地解释某些知识点的设计原理，而会更加关注在架构演进过程中为什么要用到这些技术及这些技术的适用场景及局限。

 **言简意赅，拒绝话痨**：对比国内外技术书箱，很大的差异在于国内很多作者喜欢“堆代码”，喜欢“旁征博引”，本书不求“厚”，但求“精”，对于一些显而易见或是网络中已有详尽介绍的知识点本书尽量以扩展阅读的形式呈现给读者，不占用过多篇幅介绍。

 **内容殷实，力求全面**：在保证行文紧凑的同时，本书尽量覆盖服务架构的核心点、项目实例的全流程关键点，并且会引入一些相关的扩展话题以供探讨，IT技术的发展可能是各个行业中最快的，服务架构做为最重要的IT基础性技术之一自然也在快速地进化，所以本书也会用适当的篇幅用实例介绍目前比较业界被看好的Service Mesh及Serverless技术。 

**扩展丰富，追根溯源**：本书以脚注或扩展阅读的形式引用了大量基础或发散性的解释说明做为知识体系的补充，引文力求正本清源，多为论文链接或权威网站（多见于Wikipedia、InfoQ、IBM）的一手资料。

### 本书受众

> 生命是以时间为单位的，浪费别人的时间等于谋财害命；浪费自己的时间，等于慢性自杀。
>
> ——鲁迅

 读者希望能有所收获，笔者希望能得到认可，所以有必要先说明本书的受众群。 本书适合的读者群体是：

**有一定编程经验，对服务化架构有些耳闻或实践并希望在这方面能有所提高的程序员/架构师。**

如果您：   
**没有任何编程相关经验** 那么建议先打好基础，从语言上入手更为合理  
**是一名资深应用架构师，对服务化架构非常精通** 如有兴趣欢迎带着“批判性”的态度阅读，指正其中的不足  
**对服务化架构有一定经验但欠缺某一、两点知识** 建议浏览下本书的目录，直接Google需要补强的知识点更为高效  
**想熟练使用诸如Dubbo、Spring Cloud等的服务化框架** 市面上有很多介绍这些框架的书籍，本书会花篇幅介绍框架，但术业有专攻，学会用某一框架不是本书主旨

### 约定

🔆 **核心知识点**，服务化架构中经常会遇到的问题   
📖 **扩展阅读**，由于篇幅所限，部分知识无法展开，但会给出扩展学习的相关书籍或网址   
⚠ **与知识点无关的重要说明**，了解此说明可更好地理解笔者意图   
❓ **思考点**，根据上下文描述抛出问题，引发读者思考

### 结构说明

本书以实例导向，但如果一开始就介绍项目实例未免过于唐突，所以开篇会宏观地介绍这几十年间服务架构的变迁以让读者对什么是单体、SOA及微服务有一个基本的认知。然后我们简单地介绍实战项目的背景及业务要求为后续章节做好铺垫。 

与直接上微服务相比我们有更多的项目都是有“历史”的，所以笔者会从单体架构的实现说起，了解单体架构的优势及不适用的场景进而带出微服务化改造。 

针对实战项目的微服务化改造之前会花比较多的篇幅介绍改造的各个关键点，此处说是改造但也同样适用于全新的项目。微服务化的核心分架构设计、开发测试、运维交付等三大环节，本书会重点介绍这几个环节的知识点。这些知识点偏理论，笔者会尽量带入实战项目加以说明。 

新老系统过渡的设计也是笔者被咨询比较多的问题，所以本书会花一个章节来说明相关的设计方案及如何落地。

再往后以Spring Cloud为基础介绍实战项目的微服务改造核心步骤，大部分步骤会与上文的知识点呼应，让读者也解理论的落地形式。

Vert.x与Spring Cloud类似，是笔者非常推崇的微服务框架，本书会花些篇幅加以介绍。

Service Mesh是微服务的发展方向，笔者所在的团队也有一定的实践经验，本书会以Istio这一Service Mesh主流框架做一定的实例介绍。 

如果说Service Mesh是已经进行中的微服务演进，那么再远一点，Serverless无疑是服务架构又一里程碑式的跃进，目前虽不成熟，但所有的平台架构师都应该保持关注。本文最后会以Apache OpenWhisk为Serverless框架介绍相关的使用。 

通过本书，笔者期望带读者了解到服务架构的宏观全景，其中的核心知识点及项目实践与落地，并且可以以比较前瞻性的眼光发现未来、理解未来。

### 版权协议

!\[知识共享许可协议\]\([https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)\)  
本书采用 [知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议](http://creativecommons.org/licenses/by-nc-sa/4.0/) 进行许可。
