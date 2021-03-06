# 服务划分

服务的合理划分，即服务边界的设定是微服务成功的重中之重，是所有项目实施之前必须认真思考，严肃对待的。

一个合理的服务划分应该是：

* **符合团队结构** 服务的落地与维护靠人，靠的是执行团队（包含业务、产品、技术、测试与运维团队），所以服务的设定一定是与团队结构相辅相成的，同一个系统不同的执行团队往往会有不同的且都合理的服务划分方案
* **业务边界清晰** 各服务有清晰的责任及边界，一个服务对应一块业务，服务间多为单向依赖
* **最小化地变更** 新增或变更业务上有很明确的服务对应，或是新增服务或是扩展某些服务，很少出现既可以在这个服务上实现也可以在那个服务上实现这种摸棱两可的情况，在符合上述条件前提下某一业务需求地变更受影响的服务应该尽可能地少
* **最大化地复用** 服务的复用是服务化的一个重大优势，服务设定要考虑复用的场景，在符合上述条件的前提下应该尽可能最大化地实现服务复用
* **性能稳定简洁** 上述条件更多的是业务导向，从技术上服务设定的核心要关注对性能的影响、是否稳定及架构是否简洁，是否要引入额外的中间件等

那么我们应该怎么做才能满足这些要求呢？下文笔者就带领各位一同探讨下这个问题。

## 以业务、技术、团队导向规划服务

我们必须明确的是：服务不是越细越好，服务划分的第一要素是先以业务域拆分，再以技术视角拆分，结合团队的规模、能力确定服务间的关系与边界。

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division1.png?sanitize=true)

对车贷通而言，上图是它的相对原子化的业务服务单元，理想情况以此拆分可到较细粒度的服务，但考虑两点：

* 业务上，一般而言一个需求最好只影响一个服务，这样可保证快速版本迭代，但贷款申请、审核、放款是一个完整流程的操作，如果申请中修改了填报的信息，那在审核、放款时也必定要处理或展现，所以对贷款流程的需求可能需要修改三个服务，这明显不合理
* 团队上，成员规模及技能水平有限，过细的拆分会导致开发、维护困难

所以我们重新划分下边界：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division2.png?sanitize=true)

我们划分成四个服务：基础服务包含客户信息管理、金融产品管理及用户权限管理，所有贷款流程相关的都放在贷中服务，还款、客户维系、催收等划到贷后服务，论坛独立成一个服务。这样服务边界更清晰了，但是有如下几个问题：

* 所有服务都会依赖用户权限管理
* 登录、注册、催收等都需要发短信

可见存在一些服务是公共的，针对这个情况我们可以再以技术视角做垂直拆分：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division3.png?sanitize=true)

现在我们分别从业务域及技术域上做了简单的服务边界划分，看似符合要求了，但从架构的全局观上分析还是有比较大的问题，主要体现在对业务规划缺乏理解，在业务成熟后存量客户的维系及新客户的拓展会成为一个重心，一般而言会有配套的精确化营销（或类似）系统，同期数据化经分系统也会成为核心以为各类决策提供数据支撑，上述架构没有体现出业务系统边界，因此我们再次修正下：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division4.png?sanitize=true)

团队的整体能力是要考虑一个重要因素，一般而言团队的整体能力与服务的数量成正比，反之极容易导致架构失控。

## 领域检查

现在看起来边界更清晰：明确了系统构成及各系统内的服务，但别急。由于我们这个示例比较单一，所以各系统的业务域及服务的业务边界比较明确，但在有多条产品线、多项目团队合作研发的场景下极容易出现某些功能看上去在哪个产品中做都可以的情况，这时如何划分边界就比较棘手了。

这种情况下我们可以引入领域模型。使用领域模型为服务的业务划分提供指导是个很好的开始，国内也有不少优秀的实践[Jdon](http://www.jdon.com/)。

>🔆 领域驱动设计：DDD（Domain-driven design）是一套综合软件系统分析和设计的面向对象建模方法。[详见此处](https://en.wikipedia.org/wiki/Domain-driven_design)

下图笔者所服务公司资产端数据结构的领域模型示例，通过领域建模可以很清楚地明确各实体对象的归属，进而确定业务服务的边界。

![DDD示例](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-ddd.png?sanitize=true)

领域建模对服务的划分有非常重要的指导意义，即使我们不用DDD也应该多少了解领域模型设计，反观国内IT企业大家对这块太不重视了，举一个例子：某国内知名的垂直电商公司的CTO公开讲他们订单与优惠券的设计演进，第一个版本核心结构如下：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-ddd-analysis1.png?sanitize=true)

这一版很简单也很好理解，问题在于它将优惠券与商铺绑定，但优惠券有针对商品的、商铺的和平台的，比如X商铺A商品5折、X商铺满100减20，平台满200减20等，上面的结构明显不符合要求，所以他们改成如下结构：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-ddd-analysis2.png?sanitize=true)

做法是将订单拆分成针对商品、店铺及平台（支付）的三类，一个支付订单可以包含一个或多个店铺订单，一个店铺订单可以包含一个或多个商品订单，问题解决了吗？的确从功能上看是满足了，但这种做法的后患是订单与优惠券完全绑定了，订单被优惠券绑架了，如果业务上又出现了针对不同类目的优惠（这很常见）那是不是又要加入类目级订单？

这就是典型的缺乏领域建模思维的产物，如果用领域建模会是怎样？从领域角度看这种做法是完全错误的，订单与优惠券分属于两个领域，一个是核心域（暂且这么称呼），另一个是运营活动域，原则上运营活动域是可选的，不应该为支撑可选域而对核心域做过多的侵入（从1个订单变成3个订单），所以理想情况下优惠券对订单的支持应该只是在运营活动域中处理。理解了这个后模型很简单：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-ddd-analysis3.png?sanitize=true)

引入一张订单优惠券的关联表（Order Coupon）即可，核心域只关注订单，各类活动的处理在运营活动域中操作。

就这么简单？是的，从领域处理上就是这样，但要支持我们的需求需要有些特殊的处理，在关联表要引入事务ID，同一次操作事务ID相同。相关的操作逻辑如有兴趣欢迎到互动论坛中留言讨论。

领域检查可以为我们的服务划分提供方向性的指导，使服务划分更明确、各有规划性。

## 依赖DAG检查

DAG在数学上是有向无环图，指从任何一点出发都不会回到这个点，即不存在环路，我们服务的依赖也应如此。服务间要尽量避免双向或循环依赖，否则可能会导致灾难性的后果。


![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-dag1.png?sanitize=true)

如上图所示，对应我们的真实场景下，服务4一般可为公共服务，为其上层业务（服务1、服务2、服务3）提供能力支撑。公共服务不应该依赖于上层的业务这点毋庸置疑，否则会导致公共服务不纯粹，无法独立。

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-dag2.png?sanitize=true)

而最被大家忽视的是这种：同层服务之间的相互依赖，如果服务1对服务2只有单向依赖，那么服务1的修改、测试及部署都不会影响服务2，但形成双向依赖后我们就不得不考虑服务1的变更对服务2的影响，甚至需要修改服务2相关的代码同时还要对服务2进行测试、部署，更糟糕的情况是如果服务2又依赖于其它服务，其它服务又依赖于另外的服务，那么要处理的服务就更多了。

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-dag-dependency.png?sanitize=true)

上图是某项目改造前的服务依赖，存在很严重的双向依赖，直接导致了无法自动化部署、需求响应慢等诸多问题。一个设计精良的系统会做服务分层，业务在上，基础能力在下，上层调用下层服务，下层不依赖于上层，各服务间不存在相互依赖。

依赖处理不当会带来如下问题：

* **需求响应速度变慢** 一个需求的变更可能会波及更多的服务
* **系统可用性降低** 无法制定有效的降级方案，双向依赖可能导致一个服务有问题影响整个系统的可用性
* **架构不可扩展** 牵一发而动全身，公共服务无法抽象，业务服务无法重构

具体到车贷通可能存在的情况是贷中和贷后服务中存在一定双向依赖：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-dag-cdt1.png?sanitize=true)

贷款申请时先做前置判断：如果该顾客尚有未完成的贷款则拒绝本次申请，在催收巡检时需要获取已放款的订单来确定要处理的数据，很不幸的是它们彼此存在于两个服务中。解决的方法有三种：

* **直接查询数据库而不是调用服务** 这是最直接暴力的做法，但带来的问题也很明显：如果操作的业务很复杂会导致代码冗余，即两个服务都要存有相同的代码，另外从服务隔离的角度看也不很合理
* **做数据冗余** 以上例而言，在贷中服务放款完成后将这一数据同步给贷后服务，在贷后服务中冗余一份，这样后续做催收巡检时就不需要再请求贷中服务了，当然这会带来数据同步处理的需求

>❓ **比较有争议的问题：服务间的数据库是否需要独立？**
>
>主张独立的观点认为所有调用都应该是服务接口调用，不应该直接查询非本服务所属的数据表，这样才能最大化地实现能力复用，也最能保证服务解耦，反对的观点认为这带来过多的资源消耗及精力投入，现实情况存在比较多的跨服务查询，比如贷后服务中查询用户还款列表时就需要用到贷中服务的贷款信息表，过分地强调隔离会使系统设计更为复杂，得不偿失。
>
>笔者在此不评论两个观点的是非，在实践中，笔者一般采用系统间数据隔离（分库），系统内各服务数据共享（同一库）。

* **增加抽象层** 架构设计中绝大部分问题都可以通过增加一层解决，这也笔者认为最优雅的方法。如下图，我们可以增加公共业务服务这一层，在其上实现订单服务，这样就上述的需求而言通过订单服务实现了完全的解耦

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-dag-cdt2.png?sanitize=true)

完成DAG检查后我们的服务划分演进成了如下形式：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division5.png?sanitize=true)

我们为信贷系统增加了数据服务层，添加了三个数据服务，分别提供对金融产品、申请用户、订单的数据操作。

>⚠ 增加抽象层从单纯的架构层面上无疑是最优雅的，但这也增加了服务维护难度，必须要结合团队能力综合考虑，如果团队配置相对单薄那么直接查数据库的方案短期内可能更为适合。

## 分布式事务检查

分布式事务调用的成本很高，服务拆分尽量避免产生跨服务事务，能合则合。如无法合并则优先考虑TCC或基于MQ的柔性事务，尽可能规避2PC等对性能影响很大的事务方案。TCC可完全替换2PC，但开发成本偏高，需要调用各方都同步修改以支持Try、Confirm和Cancel操作，某些场景会调用三方服务，其代码不受我们控制，此时可以考虑使用MQ实现异步消息和补偿性事务。

由于业务上要求“将财务结算由线下手工操作改为系统内自动化处理”，所以车贷通不可避免地会引入分布式事务以处理本系统与银行间的结算，如何实现下文会有专题说明。

## 性能分布检查

对于特别耗资源的操作应尽量独立。比如上文提到车贷通使用了bcrypt（一种基于Blowfish算法的散列函数，类似MD5,但Hash时极为消耗CPU），导致系统注册服务的TPS严重下降，这时就应该考虑把这个签名操作对独立成服务，为这一服务部署更多节点，并且可以为其独立购买计算优化型云主机。

车贷通需要近实时地同步GPS追踪器厂商的GPS轨迹数据，这一操作本应归属于贷后服务的贷后数据采集，但由于此操作对TPS、IO要求极高，会占用贷后服务绝大部分的资源，可重要性却次之，即使此功能临时下线对主体业务也不会有太大影响，所以有必要独立成服务，因此我们的服务划分又有新的变化：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division6.png?sanitize=true)

## 稳定（易变）性检查

一个服务中如存在稳定和不稳定的模块，应该将两者拆分。
笔者曾经为某集团的客服系统做服务化改造，此系统有很多诸如本月套餐使用情况、销售品、IVR信息、通话足迹、故障分析等功能块，各个功能块要求可以动态添加、修改，根据客户画像、历史轨迹及当前资产等情况智能推测客户呼叫的意图，动态地为客服展现相关信息，如下图：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-stability1.png?sanitize=true)

为此在设计把其独立成Widget服务，它的变更不会影响kernel服务。

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-stability2.png?sanitize=true)

车贷通运营过程中发现接入的短信服务商及三要素验证供应商响应速度太慢且不稳定，现代软件研发有太多的三方服务可选择，合理地利用三方服务可以快速高效地构建服务，但同时也带来如何管理、监控这些服务的问题，常见如短信，国内有很多供应商，提供了多样化的服务，但由于政策、三大运营商策略的关系往往没有一家短信供应商可以保证100%的触达率，所以我们在项目中往往会接入多个供应商，以一定的策略确保最大化触达，另外短信行业水很深，一般我们都会自己留一份发送记录以便做财务核对。电子合同、活体检测等作为贷款流程必不可少的一块，如果出现服务不可用对车贷通是致命的，因此也需要有多个供应商做备份有自己的调用记录。

所以这些三方服务都应该独立，服务对外提供相对统一的接口，服务内部去对接不同的三方服务，消化不同三方服务在接口、规则上的差异，确保一个三方服务不可用时可以自动切换到备用三方服务上。并且这些服务与业务无关，非常适合封装成公共服务，故我们的服务划分又可修改成：

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division7.png?sanitize=true)

## 调用链检查

服务间调用有IO消耗且不易追踪，应控制调用链路的长度。以笔者的经验，一般的请求—响应类操作应该在4层以内比较合适，比如：应用服务网关——>业务服务——>（业务）数据服务——>公共服务。

当然调用链路的长短也要看情况，比如笔者设计过的风控系统（见下图），它的一次风控决策最多就需要6层调用，请求从网关Gateway路由到流量控制器（Flow Controller），流量控制器负责在合适的时机发送请求事件到决策处理器（Decision Processor），决策处理器根据决策规则要求发布因子获取事件给因子服务（Factor）,因子服务返回对应的因子数据给决策处理器以进行规则运算并异步回调业务请求方。但这一流程中如果因子数据不存在则因子服务要先请求数据采集分发器（Collect Dispatcher），数据采集分发器分发要采集的数据给对应的采集器，采集完成后逐级返回。这一系统通过MQ实现完全异步化处理，多层调用是为服务解耦，符合上述服务划分的要求，同时又因为是异步回调的方式，对实时性要求不高，所以这样的调用链路是可接受的。

![](https://raw.githubusercontent.com/gudaoxuri/Microservices-Architecture/master/resources/images/ms-services-division-invoke1.png?sanitize=true)

**服务的划分是微服务设计的第一步，也是其成败的关键。正如笔者一直强调架构不应只聚焦于技术，人的因素，团队、项目的因素往往更重要，所以实践时遵循上述原则的同时更要灵活机动，因地制宜，原则指明的是大的方向，但具体到度的把握才是对架构师能力及经验的考验所在。**






