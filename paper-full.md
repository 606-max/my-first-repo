# 从模型驱动到数据驱动的飞行控制——方法演进与融合趋势


## 摘要

飞行控制方法正经历从模型驱动到数据驱动的深刻转型。本文以"模型依赖度递减、数据利用度递增、安全保证难度递增"三条相互联动的主线，系统综述了这一转型的演进脉络与融合趋势。首先回顾模型驱动方法的谱系——PID 与增益调度、非线性动态逆、自适应控制与鲁棒控制——指出其可认证性与建模瓶颈一体两面的本质；继而梳理数据驱动方法在神经网络控制、强化学习飞行控制与数据驱动辨识三个方向的突破，以无人机竞速中战胜人类冠军等极限任务为标志，确认数据驱动方法已触及性能上限；随后将近年最具生命力的融合架构归纳为"学习作补偿器、学习作先验、学习作预测模型"三种接口模式，提炼出"以可控的模型依赖换取数据收益、同时保持保证结构封闭"的设计准则；进而分析学习型控制器面临的安全保证难题，评述控制屏障函数、可达性分析、形式化验证与运行时保证等技术构成的设计时/运行时双重防线；最后总结工程应用进展，并展望样本效率、可认证学习路径与下一代混合智能架构等开放问题。

**关键词**：飞行控制；模型驱动；数据驱动；强化学习；模型预测控制；融合架构；安全保证；运行时保证

## Abstract

Flight control is undergoing a profound transition from model-driven to data-driven paradigms. Structured around three interlinked threads — decreasing model dependence, increasing data utilization, and growing difficulty of safety assurance — this survey reviews the evolution and convergence of flight control methods. We first trace the genealogy of model-driven approaches (PID and gain scheduling, nonlinear dynamic inversion, adaptive and robust control), showing that their certifiability and modeling bottleneck are two sides of the same coin. We then examine breakthroughs of data-driven methods in neural network control, reinforcement learning flight control, and data-driven identification, marked by superhuman drone racing. The most vibrant fusion architectures of recent years are organized into three interface patterns — learning as compensator, as prior, and as predictive model — under the design principle of trading controlled model dependence for data-derived performance while keeping the assurance structure closed. Safety assurance challenges are analyzed along the dual defense lines of design-time formal methods (control barrier functions, reachability analysis, neural network verification) and run-time assurance. Engineering applications and open problems, including sample efficiency, certifiable learning, and next-generation hybrid intelligence architectures, are discussed.

**Keywords**: flight control; model-driven; data-driven; reinforcement learning; model predictive control; fusion architecture; safety assurance; run-time assurance


# 1 引言


---

自莱特时代以来，飞行控制系统的技术形态始终由一个基本约束塑造：设计者对飞行动力学模型的掌握程度。在这个意义上，百余年飞控技术的发展史，也是一部"模型依赖史"。从早期机械链系操纵，到以 PID 反馈为代表的经典自动控制，控制品质的每一次跃升，几乎都对应着建模精度或模型利用方式的一次进步。Ziegler 与 Nichols 于 1942 年提出的临界增益整定方法，使 PID 控制器在缺乏精确模型时也能获得工程可用的参数初值[1]；此后，增益调度通过随飞行状态切换控制器参数，支撑了飞行包线的持续扩展，并构成了战斗机飞控律技术演进的工程主线之一[2]。然而，这些方法共享同一个隐含前提：存在足够准确、可持续细化的气动与结构模型，且工程师有能力在整个包线内维持模型的一致性与参数的可控性。前提一旦松动——对象更非常规、包线更宽、不确定性更强——模型驱动的边际成本便急剧上升。

进入 21 世纪第二个十年，三条技术曲线的交汇开始动摇这一前提。机载计算与感知能力的指数级增长，使"实时感知—在线学习—闭环决策"第一次具备了硬件基础；与此同时，深度学习与强化学习在序贯决策领域的突破，使控制器得以绕开显式建模、直接从数据中习得控制策略。2023 年，Kaufmann 等研制的 Swift 系统依靠仿真中的大规模深度强化学习与真实世界数据微调，在物理无人机竞速中战胜了人类世界冠军[3]——这是数据驱动方法首次在接近物理极限的任务上正面超越精细调校的模型驱动方案，被广泛视为飞控智能化转折的标志性事件。

本文以三条相互关联的主线，组织这场仍在进行中的方法论演进。**其一，模型依赖度递减**：从要求精确动力学模型的非线性动态逆，到只需不确定性界的自适应与鲁棒控制，再到仅需交互数据的学习型方法，建模负担被逐步卸载。**其二，数据利用度递增**：从被动使用历史飞行数据的辨识与增益调度，到主动采集、在线学习乃至跨任务迁移的强化学习范式，数据的角色从"校准模型的旁证"变为"控制策略的直接来源"。**其三，安全保证难度递增**：经典方法的可认证性建立在模型的可解析性之上，而神经网络控制策略缺乏同等的数学基础，迫使工程界发展形式化验证、运行时保证等补救性机制[4]。三条主线环环相扣：模型依赖的下降意味着可解析性的下降，后者正是安全论证难度上升的直接根源；而数据利用度的持续提升，则是填补模型能力缺口、夺回性能上限的必然选择。理解三者的联动关系，是把握飞控方法演进逻辑、预判下一代架构走向的关键。

基于上述框架，本文综述范围覆盖固定翼、旋翼及高机动飞行器的控制律设计与验证，重点梳理 2021—2026 年国内外代表性进展，兼及必要的经典奠基文献，共涉及文献 68 篇[5][6]。全文组织如下：第 2 节回顾模型驱动方法——PID 与增益调度、非线性动态逆、自适应与鲁棒控制——的演进脉络及其局限；第 3 节梳理数据驱动方法在神经网络控制、强化学习与数据驱动辨识三个方向的最新进展；第 4 节聚焦模型与数据的融合架构，包括残差学习、学习增强自适应控制与学习增强 MPC；第 5 节讨论安全保证与可信性挑战，涵盖控制屏障函数、可达性分析与学习系统的验证认证；第 6 节总结工程应用进展；第 7 节给出结论与展望。


# 2 模型驱动方法：精确模型的胜利与局限


---

模型驱动方法的谱系，可以按"模型在控制律中扮演的角色"排列成一条清晰的链：经典控制把模型用作参数整定的背景知识；非线性动态逆把模型用作非线性对消的前馈通道；自适应控制把模型保留为结构、仅对其参数不确定性在线补偿；鲁棒控制则把模型误差显式纳入设计目标，为最坏情形定价。本章依次考察这条链上的四个环节，并辨析每一环节在"模型依赖度"坐标上的位置。

## 2.1 经典控制：PID 与增益调度

PID 控制律以其简洁的形式

$$u(t) = K_p e(t) + K_i \int_0^t e(\tau)\,d\tau + K_d \dot{e}(t) \tag{1}$$

统治了工业控制一个世纪，航空领域亦不例外。Ziegler 与 Nichols 的临界增益整定方法[1]提供了绕开精确模型的实用起点；时至今日，对四旋翼等小型平台的系统综述仍显示，PID 及其增益调度变体凭借实现简单、调试直观、算力需求极低等优势，占据着工程应用的主力位置[7]。当飞行状态大范围变化导致固定参数失效时，增益调度成为标准应对：按调度变量（空速、迎角、动压等）预先离线整定多组参数，在线插值切换。Rugh 与 Shamma 对该范式的理论梳理[8]指出，其有效性依赖于三个隐含条件——调度变量能够充分表征动力学变化、参数表覆盖整个包线、且调度速率足够缓慢。战斗机飞控律的工程演进史[2]恰恰印证了这些条件的维系成本：包线每扩展一次，参数表的标定、试飞验证与维护工作量便随之累积。增益调度由此暴露出经典方法的第一个结构性矛盾：**模型知识不再直接进入控制律，却以更隐蔽的方式沉淀为昂贵的地面标定工作**。

## 2.2 非线性动态逆与反馈线性化

增益调度的"分段线性"思路在非线性对象面前显得笨拙，非线性动态逆（NDI）则试图一次性解决：利用模型对消对象的非线性，将任意非线性系统精确线性化。Lane 与 Stengel 于 1986 年将这一思想系统引入飞控设计[9]，此后 NDI 与时标分离结合，成为高机动战斗机的候选控制架构。然而对消的代价是误差的直接传导——气动数据偏差 1:1 地转化为控制输入偏差。Reiner 等以 μ 综合鲁棒化动态逆[10]，缓解但不消除了这一依赖。转折出现在 Sieberling 等提出的增量式动态逆（INDI）[11]：其控制输入按下式增量计算

$$\Delta\delta = G^{-1}(x)\left(\nu - \dot{x}_0\right), \qquad \delta = \delta_0 + \Delta\delta \tag{2}$$

其中 $\dot{x}_0$ 为实测角加速度。**以传感器测量替代模型线性化项**，使控制律对模型失配的敏感度骤降——Smeur 等进一步将自适应律嵌入 INDI 以处理执行器不确定性[12]；Wang 等随后补上了 INDI 闭环稳定性的一般性证明[13]，填补了该方法长期"近似鲁棒而欠严格论证"的理论短板。在应用端，INDI 已从微小型飞行器的姿态控制[12]推进到激进轨迹跟踪[14]乃至载人客机的完整设计—试飞验证[15]，而针对其内动态稳定性的系统分析[16]则厘清了方法的适用边界。值得注意的是 NDI 家族的演化方向：从 NDI 到 INDI 的二十年，本质上是一条**持续用传感数据置换模型知识**的路线——模型依赖度的下降在这里已经悄然开始，只是尚未超出模型驱动范式的基本盘。

## 2.3 自适应控制：不确定性的在线补偿

如果说动态逆仍在依赖静态模型，自适应控制则承认模型参数不可先验已知，转而在线估计与补偿。模型参考自适应控制（MRAC）为此提供了经典框架：以参考模型规定期望闭环行为，自适应律驱动实际系统跟踪其动态。这一范式的固有难题是自适应速度与鲁棒性的耦合——快速自适应放大噪声与未建模动态，慢速自适应则丧失时效。Hovakimyan 与 Cao 的 L1 自适应控制专著[17]给出了目前最系统的解法：在自适应律与控制信号之间引入低通滤波结构，将二者的设计解耦，从而在保持快速适应的同时获得确定的鲁棒边界。该方法的航空价值经受了严苛检验——在 NASA AirSTAR 无尾飞行验证机的失速与深失速区完成了全部试验卡的 L1 控制器飞行试验[18]，并进一步延伸至载人平台的非匹配不确定性场景[19]。在经典 MRAC 框架内部，Sonneveldt 等将约束处理引入自适应反步设计，实现了 F-16 全包线的非线性轨迹控制[20]。从主线视角看，自适应控制是模型依赖度下降的第一级台阶：**模型结构仍被完全信任，但参数层面的不确定性已被在线机制吸纳**。

## 2.4 鲁棒控制：面向最坏情形的设计

与自适应控制的"在线补偿"互补，鲁棒控制选择"离线兜底"：将模型误差显式纳入设计，保证控制器在最坏不确定性下依然稳定。$H_\infty$ 方法以扰动抑制的 inf-norm 为指标，$\mu$ 综合进一步刻画结构化不确定性；二者在飞控中的工程价值已由对比研究定量确认[21]。当对象沿包线呈现强参数变化时，LPV（线性变参数）方法将调度变量正式纳入系统描述，使增益调度从"工程技艺"升级为"系统理论"：切换 LPV 控制器配合状态重置机制解决了 F-16 纵向控制中多凸域切换的稳定性难题[22]；输入协方差约束 LPV 设计则在翼身融合布局飞机上同时保证 $H_\infty$ 性能，用于颤振与结构振动的抑制[23]。鲁棒控制的账目同样清晰：**它用设计阶段的计算复杂性置换了不确定性的风险溢价**——模型依赖度并未下降，而是被转化为对不确定性集合的精确刻画能力；集合刻画得越保守，性能损失越大。

## 2.5 小结：可认证性与建模瓶颈的共存

纵观本章四类方法，模型驱动的核心资产与核心负债实为一体两面。资产方面：闭环行为具有 closed-form 的数学描述，稳定裕度、鲁棒界均可离线证明——这正是民航适航体系能够接纳它们的根本原因；工程工具链成熟，从设计、仿真到试飞验证的流程标准化程度高。负债方面：其一，建模成本随对象非常规化而陡增，气动辨识、风洞与试飞标定构成可观的入门门槛；其二，不确定性覆盖有限，未建模动态（如大迎角分离流、地面效应）恰好落在方法保证的盲区；其三，包线维护负担随任务扩展持续累积[2]。值得强调的是，本章考察的方法演化——从增益调度到 INDI 再到 L1——已经显示模型驱动范式内部的"去模型化"压力：每一代改进都在用测量数据、在线估计或不确定性集合来稀释对先验模型的依赖。当这一稀释过程触及结构层面——模型本身不再可信、或根本不存在时，数据驱动方法便从补充上升为主角。这正是下一章的主题。


# 3 数据驱动方法：从辨识到学习


---

如果说模型驱动方法的历史是一部"如何更好地利用先验知识"的历史，数据驱动方法则提出了一个更激进的问题：当先验知识不可靠、不完整或根本不存在时，控制系统能否直接从运行数据中习得所需的行为？本章沿"数据在控制回路中扮演的角色"考察三个方向——神经网络对不确定性的学习补偿、强化学习对控制策略的直接习得、以及数据驱动辨识对模型本身的在线重构，并评估这一范式在性能上限与安全底线之间的真实处境。

## 3.1 神经网络飞行控制

神经网络进入飞控的最初动机并非取代模型，而是修补模型。Emami 等对该领域的系统综述[24]勾勒出清晰的谱系：从早期将网络用作不确定性在线近似的反馈误差学习与伪控制偏置，到神经反步、再到与自适应最优控制的融合，神经网络的数学角色始终是**充当模型无法表达的那部分动力学的万能逼近器**。这一谱系中一个容易被忽视的进展是知识的可复用性：Yang 等的确定性学习框架证明，闭环运行中习得的不确定性信息能够以常值神经网络权值的形式持久存储，并在后续任务中直接调用[25]——这意味着学习型控制器第一次具备了"经验积累"能力，而这是任何固定参数的模型驱动控制器在原理上不具备的。

性能上限的验证则来自物理极限任务。引言所述的 Swift 系统之外，Song 等以时间最优竞速为基准，在同一硬件上直接对比了模型最优控制与强化学习[26]：RL 策略在训练分布内的跟踪精度与抗扰性略优于精心设计的最优控制，但在分布外扰动下呈现更陡峭的性能衰减。这组对照实验的价值在于其诚实性——它同时标定了数据驱动方法的优势边界与脆弱边界，拒绝了两类方法支持者各自的过度承诺。

## 3.2 强化学习飞行控制

强化学习将数据驱动的逻辑推到极致：不仅学习不确定性，而是直接学习从观测到动作的完整映射。其工程可行性在过去五年经历了一次决定性验证，验证场域集中在对控制品质要求最苛刻的无人机竞速任务上。Kaufmann 等的早期工作建立了从图像到航点与期望速度的端到端敏捷飞行框架[27]；Loquercio 等则以"特权学习"架构——训练时访问仿真内部状态、部署时仅依赖机载传感——实现了复杂野外环境下的高速避障飞行，且无需任何真实世界微调[28]。打通仿真到现实的关键方法论是域随机化：Loquercio 等证明，对仿真中动力学与视觉要素的大范围随机化，可以换取策略对现实参数漂移的鲁棒性，实现敏捷飞行的首次零样本 sim-to-real 迁移[29]。在此基础上，Song 等完成了感知—规划—控制一体训练的闭环时间最优竞速[30]，Kaufmann 等则通过系统性基准测试回答了"策略应该输出什么"这一设计问题——推力加体速率的抽象动作空间在迁移性能上显著优于力矩级输出[31]。学习与模型方法的混合架构（学习负责估计、模型负责规划）在同一时期即取得竞赛冠军[32]，预示了第四章的主题。

应用面同步拓宽：完整的自主竞速系统（含感知、规划、控制闭环）已在大型竞赛中夺冠并公开了全流程技术报告[33]；深度 RL 低层控制器在 6 m/s 真机轨迹跟踪中展现出超越 PID 的抗扰能力[34]；基于 DDPG 的姿态控制器在小幅干扰的恢复速度上亦优于经典 PID 方案[35]；免人工调参的端到端控制器可在微型平台上部署即用[36]。中文核心期刊的飞行试验亦跟进：基于深度强化学习的空战机动决策完成了人机对抗飞行验证[37]，具备防撞安全约束的集群协同控制则将 DRL 推向多机场景[38]。针对真机交互成本与风险，离线强化学习以悲观估计约束策略保守性，避免了在线探索的试飞代价[39]。整体来看，RL 飞控已完成从"仿真可行性演示"到"真机性能验证"的跨越，其数据利用度——从仿真交互、真机遥测到人类飞行员演示——达到了飞控历史上前所未有的广度。

## 3.3 数据驱动辨识与状态估计

第三条路径回到了模型的源头：如果模型不可信，能否用数据直接生成更好的模型？Ahmed 等以自适应时序模型与高斯过程回归构建"飞行感觉"（fly-by-feel）状态感知，使机载系统能从自身传感数据中实时推断气动状态[40]；Wild 进一步以无监督聚类从真实飞行数据中辨识飞行状态，彻底摆脱了对标注数据与预设模型结构的依赖[41]。最具代表性的是 Bauersfeld 等的 NeuroBEM：以神经网络混合传统一阶气动模型学习四旋翼全包线气动力，其预测精度大幅超越任何解析模型[42]。这类工作的深层意义在于其架构姿态——**学习产物以"更好的模型"形式交付给传统控制回路**，数据驱动在此充当模型驱动的供给者而非替代者，这一接口设计正是第四章融合架构的直接先声。

## 3.4 小结：性能上限已证，安全底线未决

本章三个方向共同确认了一个事实：数据驱动方法在性能维度上已经触及并部分突破了模型驱动的天花板——极限任务中战胜人类[3]、超越最优控制[26]、预测精度碾压解析气动模型[42]。但其负债同样结构性地存在。其一，样本效率：RL 策略的训练动辄需要仿真中数百万次交互，真实系统难以承担，域随机化与离线学习均为这一矛盾的缓解而非消解[29][39]。其二，分布外泛化：F4 的对照实验明确显示，训练分布之外的扰动会引发陡峭的性能衰减——而飞行器的使命恰恰是进入训练时未曾预想的情形。其三，也是最具约束性的一条：**学习型策略没有与经典方法对等的 closed-form 稳定裕度可写进审定文件**。数据利用度的每一次跃升，都以可解析性的剥蚀为代价——这正是三主线框架所预言的安全保证难度递增。下一章将考察工程界如何在两条范式的交界处重建安全与性能的平衡。


# 4 融合架构：模型与数据的协同


---

前两章的对垒容易造成一种误读：模型驱动与数据驱动是非此即彼的竞争关系。工程实践给出的答案恰恰相反——近五年最具生命力的飞控架构，几乎全部位于两个范式的交界带上。融合的本质不是折中，而是**分工**：模型承担结构性的保证（稳定性、约束满足、可认证性），数据承担内容性的精度（真实气动力、真实扰动、真实性能极限）。按学习产物进入控制回路的方式，融合架构可以整理为三种接口模式——学习作补偿器、学习作先验、学习作预测模型。本章依次考察。

## 4.1 残差学习：学习作补偿器

最自然的融合接口，是让神经网络学习基线模型与真实动力学之间的残差，再将其作为补偿项嵌入控制回路。Shi 等的 Neural-Lander 是这一模式的干净示范：以神经网络学习四旋翼近地着陆时的地面效应残差气动，将补偿项叠加在经典反馈控制之上，并借助深度网络的利普希茨约束给出闭环稳定性证明[43]——学习在这里被"关进了"稳定性框架的笼子。O'Connell 等的 Neural-Fly 将该模式推向系统性高度：离线通过元学习获得风况相关的残差基函数库，在线以 MRAC 式复合自适应律实时组合基函数，仅用 12 分钟的飞行数据便实现了强风条件下此前无法达到的精确轨迹跟踪，且整个方案带有李雅普诺夫稳定性保证[44]。这一架构的深层效率在于：**物理先验把学习问题的维度从"完整动力学"压缩到"残差"**，数据需求随之骤降——12 分钟对第 3 章动辄百万次仿真交互的训练量，是两个数量级的差别。与此呼应，NeuroBEM 的混合气动模型[42]（见 3.3 节）则从建模侧展示了同一分工原则：解析结构负责可解释的骨架，神经网络负责数据密集的残差。

## 4.2 学习增强的自适应与鲁棒控制

第二类接口让学习产物进入经典保证框架的内部。第 2 章所述的 L1 自适应控制在安全关键飞行验证中的成功[18]，及其向载人平台非匹配不确定性的扩展[19]，已经勾勒出"保证框架吸纳在线估计"的模板；自适应增量动态逆[12]同样是在 INDI 的鲁棒骨架上叠加在线参数估计。学习方法的加入使这一模板从"估计参数"升级为"估计函数"：确定性学习框架证明闭环中习得的不确定性可以固化为常值网络权值并在后续任务中复用[25]，这实际上把自适应控制从"每次飞行从零开始"改造为"跨飞行的知识积累"——保证结构不变，保证所针对的不确定性描述被持续精化。

## 4.3 学习增强 MPC：学习作预测模型

第三类接口直接改造模型预测控制（MPC）的内核。MPC 以显式优化处理约束与多目标的能力无可替代，但其预测模型的质量构成性能硬上限：Foehn 等证明，仅当气动力被精确建模时，多航点飞行的时间最优解才可被求解[45]——真实的阻尼与拖曳效应恰恰是解析模型最难覆盖的部分。Rosolia 与 Borrelli 的学习 MPC 框架为此提供了奠基性方案：利用迭代任务的重复性，从历史数据在线收缩终端集合，使闭环性能随运行次数单调逼近最优[46]。Torrente 等则以学习型预测模型（含阻尼、拖曳等真实效应）替换解析模型嵌入 MPC，在高速轨迹跟踪上兑现了"模型越好、飞行越快"的承诺[47]。在时间最优飞行这个基准场景中，模型预测轮廓控制（MPCC）将全局轨迹与实时控制统一于一个优化问题[48]，自适应 NMPC 进一步在载荷与气动参数漂移下维持跟踪精度[49]；最新的 MPCC++ 更在轮廓控制中显式引入安全约束，实现"带保证的时间最优飞行"[50]——性能与安全的融合在同一框架内完成，直接衔接第五章的主题。环绕这一主线的系统性工作——NMPC 与微分平坦控制的定量对比[51]、RL 参数化 MPC 的稳定性与鲁棒性保证[52]、学习 MPC 的双重路径（监督学习与强化学习）理论梳理[53]、以及面向飞行器应用的 MPC 体系综述[54]——共同确认了该方向的成熟度。

## 4.4 小结：以可控的模型依赖换取数据收益

三种接口模式——补偿器[44][43]、先验[12][18][19]、预测模型[46][47]——构成一条清晰的融合谱系，其共同设计准则可以概括为一句话：**把模型的每一分可信性兑换成数据的每一分性能收益，同时让保证结构保持封闭**。第 3 章的对照实验[26]与混合架构的竞赛战绩[32]从两端支持了这一范式的竞争力：纯学习方法赢在分布内的极限性能，纯模型方法赢在分布外的可预期性，融合架构的目标则是同时占据两条曲线的上方。需要指出的是，融合并未消解安全论证的难题，只是改变了它的形状：当控制律中嵌入网络权值，"稳定性证明"的对象从有限维参数空间变成了函数空间，经典审定工具对此并不适配。模型—数据协同架构能在多大程度上获得适航体系的接纳，取决于第五章将要讨论的安全保证技术能否追上融合架构自身的演化速度。


# 5 安全保证与可信性


---

前四章反复出现的判断——模型依赖度递减伴随可解析性剥蚀——在本章上升为正面议题。安全保证难题的新形状可以精确表述为：经典审定体系要求控制律的闭环性质具有可离线证明的数学形式，而学习型控制器的策略存在于函数空间中，其行为边界无法由有限维参数的不等式组完整刻画。工程界对此的回应形成了两条防线：**设计时**的形式化安全机制，与**运行时**的保证架构。本章依次考察，并最终回到三主线框架收拢全文论证。

## 5.1 形式化安全机制：CBF 与可达性分析

安全强化学习的奠基性综述将现有方法归入两大范式——修正最优准则（将安全代价写入目标）与修正探索过程（约束策略搜索空间）[55]；Brunke 等的机器人安全学习综述进一步把控制屏障函数（CBF）、可达性分析与安全强化学习纳入统一分类[4]。在飞控语境下，可达性分析与 CBF 是两条主要的形式化路径。Hamilton–Jacobi（HJ）可达性分析能够给出最坏情形下的最大可控安全集，其计算复杂度曾长期限制在线应用；Herbert 等通过状态空间分解、热启动与自适应网格，使安全集的在线学习更新成为可能——在 10 维四旋翼风场干扰场景中完成了演示[56]。更近的理论工作开始打通两条路线：将 HJ 可达性与强化学习统一到运行代价表述下，并证明值函数的粘性解收敛性，为"可学习的安全滤波器"提供了理论根基[57]。CBF 路线则以更低的在线代价换取局部保证：基于 CBF-QP 的安全滤波器可以对接任意商用自驾仪，以最小干预约束目标追踪与避撞行为[58]，并已在学习增强 MPC 的安全约束版本中落地[50]。针对学习过程的另一半风险——探索本身的不安全，面向飞控的安全探索算法保证了策略搜索全程不违反安全约束[59]。这些机制与前述带有李雅普诺夫保证的残差学习架构[44]共同表明：**形式化保证与学习并非不可兼得，但每一次融合都伴随保守性与适用范围的取舍**。

## 5.2 学习控制器的验证、认证与测试评估

形式化机制解决"能否安全"，认证体系回答"如何被监管接受"。这一环节的标志性起点是 Reluplex：首个可扩展验证 ReLU 网络性质的 SMT 求解器，以空中防撞系统 ACAS Xu 为基准，确立了航空神经网络安全验证的研究范式[60]；后续工作将神经网络验证工具与可达性分析结合，为神经网络化的防撞逻辑提供了更完整的安全证明路径[61]。然而验证工具的规模上限与机载系统认证的合规要求之间，仍隔着体系性鸿沟：以跑道标志识别网络为案例的研究表明，机器学习机载系统在 DO-178C 设计保证框架下缺乏明确的目标与 DAL 分级对应关系[62]。监管侧的回应已在推进——欧盟航空安全局与工业界联合提出的神经网络设计保证概念（CoDANN），以"学习保证"（learning assurance）的 V 型流程给出了 AI 适航认证的首个体系化构建块[63]。与设计时验证互补的，是运行时保证（RTA）路线：ASTM F3269-21 标准确立了以简单可验证的后备控制器监视并约束复杂功能的架构范式，为包含学习组件的飞控功能提供了设计时保证之外的现实合规路径[64]。两条防线的分工由此清晰：形式化验证覆盖小规模关键性质，运行时保证兜底大规模不可验证部分——**认证难题被从"证明整个网络正确"转化为"证明监督架构正确"**，这是目前最具工程可行性的妥协。

## 5.3 三条主线在安全维度上的再审视

回到全文框架，本章的发现可以凝练为一条因果链。模型依赖度的递减（主线一）意味着可解析性的剥蚀：即便是模型驱动阵营内部，INDI 的内动态稳定性[16]与闭环稳定性证明[13]也表明安全论证从不免费；数据驱动方法只是把这一代价放大了一个量级——学习型策略的分布外行为缺乏天然边界，而飞行器的使命恰恰是进入未曾预想的情形，连动作空间的抽象方式都会影响迁移后的安全边界[31]。数据利用度的递增（主线二）在换取性能的同时加深了这一困境。因此安全保证难度的递增（主线三）不是方法演进的偶然副产品，而是三主线联动的必然结果；工程界的对策——设计时形式化与运行时保证的双重防线[56][64]——本质上是在为剥蚀掉的可解析性寻找替代物。下一代飞控架构的竞争力，将越来越多地取决于这种"保证重建"能力而非单纯的性能数字。


# 6 工程应用进展


---

方法演进的最终检验场是型号与飞行试验。本章按平台类别梳理前述方法的落地证据。试验基础设施方面，开源开放硬件平台 Agilicious 同时支持模型驱动控制与学习型策略的快速部署[65]，显著降低了飞控算法从实验室到真机飞行的门槛。模型驱动方法的工程深度以载人平台为标杆：INDI 控制律在载人客机上完成了从设计到飞行试验的全流程验证[15]，L1 自适应控制在 NASA 无尾验证机的失速与深失速区完成全部试验卡[18]并延伸至载人平台[19]。

数据驱动方法的飞行验证重心在小型高敏捷平台：Swift 系统战胜人类世界冠军[3]与 AlphaPilot 竞赛系统[33]分别代表了学习方法在物理极限任务与系统级工程上的当前水平。面向产业化的新构型同样在快速演进：倾转旋翼 eVTOL 的建模—辨识—增益优化完整流程[66]展示了传统工程方法在电动垂直起降领域的持续适用性；高速直升机与民用高速旋翼飞行器的关键技术展望[67][68]则勾勒了高机动旋翼平台对飞控提出的构型级挑战。值得记录的产业里程碑——如无人驾驶载人 eVTOL 的型号合格证取证与跨海跨城首飞——均有公开报道，属事实性叙述而非学术文献，本文在正文中以新闻来源注明，不计入参考文献。监管与标准层面，运行时保证架构标准[64]与神经网络设计保证概念[63]已为含学习组件的飞控系统提供了进入适航体系的现实通道，工程界"先上系统、再补认证"的探索由此获得合规框架。

综合来看，工程应用的分布呈现清晰的梯度：模型驱动方法把守载人、适航、大构型的纵深；数据驱动方法在小平台、极限任务上完成突破；融合架构在两者之间快速填充。这一梯度本身就是三主线框架在产业维度的投影。


# 7 结论与展望


---

## 7.1 主要结论

本文以"模型依赖度递减、数据利用度递增、安全保证难度递增"三条相互联动的主线，综述了飞行控制方法从经典到智能的演进。回望全文，三组结论可以成立。其一，模型驱动方法的演化史内部已经蕴含"去模型化"的方向：从增益调度的参数表、动态逆的精确对消，到 INDI 以测量置换模型知识、L1 自适应解耦自适应速度与鲁棒性，模型驱动的每一次自我革新都在稀释对先验模型的依赖，同时保住 closed-form 的可认证性。其二，数据驱动方法在性能维度已触及物理极限——极限竞速中战胜人类冠军[3]、优于精调的最优控制——其负债（样本效率、分布外泛化、无对等安全证书）同样是结构性的。其三，融合架构以"学习作补偿器、先验、预测模型"三种接口模式成为当前主流，其设计准则可概括为：以可控的模型依赖换取数据收益，同时让保证结构保持封闭。三主线并非平行排列，而是一条因果链的三个侧面：模型依赖的下降导致可解析性的剥蚀，后者正是安全保证难度上升的根源，而数据利用度的提升则是这一进程的驱动与补偿。

## 7.2 开放问题与展望

面向下一个五年，三个问题的走向将决定架构选择。第一，样本效率与安全性的联合权衡：域随机化、离线强化学习与元学习残差均是对矛盾的局部缓解，能否在保证框架内完成样本高效的安全学习，是理论层面最核心的缺口。第二，可认证的学习控制路径：形式化验证的规模上限与运行时保证的保守性之间的空隙，需要监管科学（如 CoDANN 流程[63]）与验证工具的同步扩张；"证明监督架构而非证明整个网络"的思路[4]有望成为中期的现实均衡点。第三，下一代混合智能架构的形态：竞赛与工程实践均显示，感知、规划、控制的学习化程度存在此消彼长的张力，端到端与分层架构之争尚无定论[5]；而强化学习在航空应用中的可靠性缺口——验证、泛化与人在回路——仍需系统性攻关[6]。可以预期的是，三条主线将继续联动推进：模型依赖度还会下降，但下降的终点不是模型的消亡，而是模型角色的再度迁移——从控制律的构成要素，退居为学习系统的先验、保证的锚点与安全的兜底。守住这一角色的方法论，正是飞行控制在走向智能的同时不失去"可飞"资格的关键。


# 参考文献

1. ZIEGLER J G, NICHOLS N B. Optimum settings for automatic controllers[J]. Transactions of the ASME, 1942, 64: 759-768.
2. KIM C, JI C, KOH G, et al. Review on flight control law technologies of fighter jets for flying qualities[J]. International Journal of Aeronautical and Space Sciences, 2023, 24: 209-236.
3. KAUFMANN E, BAUERSFELD L, LOQUERCIO A, et al. Champion-level drone racing using deep reinforcement learning[J]. Nature, 2023, 620(7976): 982-987.
4. BRUNKE L, GREEFF M, HALL A W, et al. Safe learning in robotics: from learning-based control to safe reinforcement learning[J]. Annual Review of Control, Robotics, and Autonomous Systems, 2022, 5: 411-444.
5. HANOVER D, LOQUERCIO A, BAUERSFELD L, et al. Autonomous drone racing: a survey[J]. IEEE Transactions on Robotics, 2024, 40: 3044-3067.
6. RAZZAGHI P, TABRIZIAN A, GUO W, et al. A survey on reinforcement learning in aviation applications[J]. Engineering Applications of Artificial Intelligence, 2024, 136: 108911.
7. LOPEZ-SANCHEZ I, MORENO-VALENZUELA J. PID control of quadrotor UAVs: a survey[J]. Annual Reviews in Control, 2023, 56: 100900.
8. RUGH W J, SHAMMA J S. Research on gain scheduling in automatic control[J]. Automatica, 2000, 36(10): 1401-1425.
9. LANE S H, STENGEL R F. Flight control design using nonlinear inverse dynamics[C]//Proceedings of the American Control Conference. IEEE, 1986: 587-596.
10. REINER J, BALAS G J, GARRARD W L. Flight control design using robust dynamic inversion and time-scale separation[J]. Automatica, 1996, 32(11): 1493-1504.
11. SIEBERLING S, CHU Q P, MULDER J A. Robust flight control using incremental nonlinear dynamic inversion and angular acceleration prediction[J]. Journal of Guidance, Control, and Dynamics, 2010, 33(6): 1732-1742.
12. SMEUR E J J, CHU Q P, DE CROON G C H E. Adaptive incremental nonlinear dynamic inversion for attitude control of micro air vehicles[J]. Journal of Guidance, Control, and Dynamics, 2016, 39(3): 450-461.
13. WANG X, VAN KAMPEN E J, CHU Q P, et al. Stability analysis for incremental nonlinear dynamic inversion control[J]. Journal of Guidance, Control, and Dynamics, 2019, 42(5): 1116-1129.
14. TAL E, KARAMAN S. Accurate tracking of aggressive quadrotor trajectories using incremental nonlinear dynamic inversion and differential flatness[J]. IEEE Transactions on Control Systems Technology, 2021, 29(3): 1203-1218.
15. GRONDMAN F, LOOYE G, KUCHAR R, et al. Design and flight testing of incremental nonlinear dynamic inversion-based control laws for a passenger aircraft[C]//2018 AIAA Guidance, Navigation, and Control Conference. AIAA, 2018.
16. ALAM M, CELIKOVSKY S. On the internal stability of non-linear dynamic inversion: application to flight control[J]. IET Control Theory & Applications, 2017, 11(12): 1849-1861.
17. HOVAKIMYAN N, CAO C. L1 Adaptive Control Theory: Guaranteed Robustness with Fast Adaptation[M]. Philadelphia: SIAM, 2010.
18. HOVAKIMYAN N, CAO C, KHARISOV E, et al. L1 adaptive control for safety-critical systems[J]. IEEE Control Systems Magazine, 2011, 31(5): 54-104.
19. ACKERMAN K A. L1 adaptive control flight testing and extension to nonlinear reference systems with unmatched uncertainty[D]. Urbana-Champaign: University of Illinois at Urbana-Champaign, 2021.
20. SONNEVELDT L, CHU Q P, MULDER J A. Nonlinear flight control design using constrained adaptive backstepping[J]. Journal of Guidance, Control, and Dynamics, 2007, 30(2): 322-336.
21. SANTOSO F, LIU M, EGAN G. H2 and H∞ robust autopilot synthesis for longitudinal flight of a special unmanned aerial vehicle: a comparative study[J]. IET Control Theory & Applications, 2008, 2(7): 583-594.
22. LU B, WU F, KIM S. Switching LPV control of an F-16 aircraft via controller state reset[J]. IEEE Transactions on Control Systems Technology, 2006, 14(2): 267-277.
23. HE T, AL-JIBOORY A K, ZHU G G, et al. Application of ICC LPV control to a blended-wing-body airplane with guaranteed H∞ performance[J]. Aerospace Science and Technology, 2018, 81: 88-98.
24. EMAMI S A, CASTALDI P, BANAZADEH A. Neural network-based flight control systems: present and future[J]. Annual Reviews in Control, 2022, 53: 97-137.
25. YANG Q, ZHANG F, WANG C. Deterministic learning-based neural PID control for nonlinear robotic systems[J]. IEEE/CAA Journal of Automatica Sinica, 2024, 11(5): 1227-1238.
26. SONG Y, ROMERO A, MÜLLER M, et al. Reaching the limit in autonomous racing: optimal control versus reinforcement learning[J]. Science Robotics, 2023, 8(82): eadg1462.
27. KAUFMANN E, LOQUERCIO A, RANFTL R, et al. Deep drone racing: learning agile flight in dynamic environments[C]//Proceedings of the 2nd Conference on Robot Learning (CoRL). PMLR, 2018, 87: 133-145.
28. LOQUERCIO A, KAUFMANN E, RANFTL R, et al. Learning high-speed flight in the wild[J]. Science Robotics, 2021, 6(59): eabg5810.
29. LOQUERCIO A, KAUFMANN E, RANFTL R, et al. Deep drone racing: from simulation to reality with domain randomization[J]. IEEE Transactions on Robotics, 2020, 36(1): 1-14.
30. SONG Y, STEINWEG M, KAUFMANN E, et al. Autonomous drone racing with deep reinforcement learning[C]//2021 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS). IEEE, 2021: 1205-1212.
31. KAUFMANN E, BAUERSFELD L, SCARAMUZZA D. A benchmark comparison of learned control policies for agile quadrotor flight[C]//2022 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2022: 10504-10510.
32. KAUFMANN E, GEHRIG M, FOEHN P, et al. Beauty and the beast: optimal methods meet learning for drone racing[C]//2019 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2019: 690-696.
33. FOEHN P, BRESCIANINI D, KAUFMANN E, et al. AlphaPilot: autonomous drone racing[J]. Autonomous Robots, 2022, 46(1): 307-320.
34. CHEN S, LI Y, LOU Y, et al. Aggressive and robust low-level control and trajectory tracking for quadrotors with deep reinforcement learning[J]. Neural Computing and Applications, 2025, 37(3): 1223-1240.
35. KIM J, JUNG S. Enhancing UAV stability: a deep reinforcement learning strategy[C]//2024 International Conference on Electronics, Information, and Communication (ICEIC). IEEE, 2024.
36. DO T, MUNG N X, HONG S K. Deep reinforcement learning-based quadcopter controller: a practical approach and experiments[EB/OL]. (2024-06-13)[2026-09-27]. https://arxiv.org/abs/2406.08815.
37. 章胜, 周攀, 何扬, 等. 基于深度强化学习的空战机动决策试验[J]. 航空学报, 2023, 44(10): 128094.
38. 蔡云鹏, 周大鹏, 丁江川. 具有防撞安全约束的无人机集群智能协同控制[J]. 航空学报, 2024, 45(5): 529683.
39. ZHAO H, FU H, YANG F, et al. Data-driven offline reinforcement learning approach for quadrotor's motion and path planning[J]. Chinese Journal of Aeronautics, 2024, 37(11): 386-397.
40. AHMED S, AMER A, VARELA C A, et al. Data-driven state awareness for fly-by-feel aerial vehicles via adaptive time series and Gaussian process regression models[C]//Dynamic Data Driven Applications Systems (DDDAS) Workshop, 2020.
41. WILD G. AI-based flight state identification for aerospace control systems[C]//AIAA Aviation Forum, AIAA Paper 2026-4755. AIAA, 2026.
42. BAUERSFELD L, KAUFMANN E, FOEHN P, et al. NeuroBEM: hybrid aerodynamic quadrotor model[C]//Robotics: Science and Systems (RSS). 2021.
43. SHI G, SHI X, O'CONNELL M, et al. Neural lander: stable drone landing control using learned dynamics[C]//2019 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2019.
44. O'CONNELL M, SHI G, SHI X, et al. Neural-Fly enables rapid learning for agile flight in strong winds[J]. Science Robotics, 2022, 7(66): eabm6597.
45. FOEHN P, ROMERO A, SCARAMUZZA D. Time-optimal planning for quadrotor waypoint flight[J]. Science Robotics, 2021, 6(56): eabh1221.
46. ROSOLIA U, BORRELLI F. Learning model predictive control for iterative tasks: a data-driven control framework[J]. IEEE Transactions on Automatic Control, 2018, 63(7): 1883-1896.
47. TORRENTE G, KAUFMANN E, FOEHN P, et al. Data-driven MPC for quadrotors[J]. IEEE Robotics and Automation Letters, 2021, 6(2): 3769-3776.
48. ROMERO A, SUN S, FOEHN P, et al. Model predictive contouring control for time-optimal quadrotor flight[J]. IEEE Transactions on Robotics, 2022, 38(6): 3340-3356.
49. HANOVER D, FOEHN P, SUN S, et al. Performance, precision, and payloads: adaptive nonlinear MPC for quadrotors[J]. IEEE Robotics and Automation Letters, 2022, 7(2): 690-697.
50. KRINNER M, ROMERO A, BAUERSFELD L, et al. MPCC++: model predictive contouring control for time-optimal flight with safety constraints[C]//Robotics: Science and Systems (RSS). 2024.
51. SUN S, ROMERO A, FOEHN P, et al. A comparative study of nonlinear MPC and differential-flatness-based control for quadrotor agile flight[J]. IEEE Transactions on Robotics, 2022, 38(6): 3357-3373.
52. KORDABAD A B, REINHARDT D, ANAND A S, et al. Reinforcement learning for MPC: fundamentals and current challenges[C]//IFAC-PapersOnLine, 2023, 56(2): 5773-5780.
53. SEEL K. Learning for model predictive control[D]. Trondheim: Norwegian University of Science and Technology (NTNU), 2023.
54. 李苑, 刘双喜, 杜兆波, 等. 模型预测控制及其在飞行器系统中的应用综述[J]. 国防科技大学学报, 2026, 48(2): 144-162.
55. GARCÍA J, FERNÁNDEZ F. A comprehensive survey on safe reinforcement learning[J]. Journal of Machine Learning Research, 2015, 16: 1437-1480.
56. HERBERT S L, CHOI J J, QAZI S, et al. Scalable learning of safety guarantees for autonomous systems using Hamilton-Jacobi reachability[C]//2021 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2021.
57. SOLANKI P, EL-HAJJ I, VAN BEERS J, et al. Unifying Hamilton-Jacobi reachability and reinforcement learning[EB/OL]. (2026-01-12)[2026-09-27]. https://arxiv.org/abs/2601.08050.
58. PANJA P, HOAGG J B, BAIDYA S. Control barrier function based UAV safety controller in autonomous airborne tracking and following systems[EB/OL]. (2023-12-28)[2026-09-27]. https://arxiv.org/abs/2312.17215.
59. MANNUCCI T, VAN KAMPEN E J, DE VISSER C, et al. Safe exploration algorithms for reinforcement learning controllers[J]. IEEE Transactions on Neural Networks and Learning Systems, 2018, 29(4): 1069-1081.
60. KATZ G, BARRETT C, DILL D L, et al. Reluplex: an efficient SMT solver for verifying deep neural networks[C]//Computer Aided Verification (CAV), LNCS 10426. Cham: Springer, 2017: 97-117.
61. JULIAN K D, KOCHENDERFER M J. Guaranteeing safety for neural network-based aircraft collision avoidance systems[C]//2019 IEEE/AIAA 38th Digital Avionics Systems Conference (DASC). IEEE, 2019: 1-10.
62. DMITRIEV K, SCHUMANN J, HOLZAPFEL F. Toward design assurance of machine-learning airborne systems[C]//AIAA Aviation Forum. AIAA, 2021.
63. EUROPEAN UNION AVIATION SAFETY AGENCY, DAEDALEAN. Concepts of design assurance for neural networks (CoDANN)[R]. Cologne: EASA, 2020.
64. ASTM INTERNATIONAL. ASTM F3269-21: Standard practice for methods to safely bound behavior of aircraft systems containing complex functions using run-time assurance[S]. West Conshohocken: ASTM International, 2021.
65. FOEHN P, KAUFMANN E, ROMERO A, et al. Agilicious: open-source and open-hardware agile quadrotor for vision-based flight[J]. Science Robotics, 2022, 7(67): eabl6259.
66. SPENCER C T. Development, modeling, identification, and control of tilt-rotor eVTOL aircraft[D]. Logan: Utah State University, 2024.
67. 邓景辉. 高速直升机关键技术与发展[J]. 航空学报, 2024, 45(9): 529085.
68. 吴希明, 吕乐丰, 张广林. 民用高速旋翼飞行器发展战略分析及关键技术展望[J]. 南京航空航天大学学报, 2022, 54(5): 827-835.
