# 文献调研草稿：《从模型驱动到数据驱动的飞行控制——方法演进与融合趋势》

> 说明：本稿按大纲章节分组列出文献，组内临时编号（正式编号由主流程统一排序）。
> 每条含：① GB/T 7714 顺序编码制条目；② 一句话中文要点；③ 建议支撑章节。
> 所有条目均经 WebSearch 结果交叉印证（标题、作者、载体、年份）；个别卷期页码未在检索结果中完整给出者，以"※"标注"待核"。
> 完成检索约 30 轮，覆盖全部 15 个规定主题。

---

## 1 引言（综述与脉络类）

**[A1]** KIM C, JI C, KOH G, et al. Review on flight control law technologies of fighter jets for flying qualities[J]. International Journal of Aeronautical and Space Sciences, 2023, 24: 209-236.
- 要点：系统回顾了 20 世纪 70 年代以来战斗机（含高机动型号）飞控律技术从经典 SISO、特征结构配置到 NDI 的工程演进轨迹。
- 支撑章节：1、2.5

**[A2]** BRUNKE L, GREEFF M, HALL A W, et al. Safe learning in robotics: from learning-based control to safe reinforcement learning[J]. Annual Review of Control, Robotics, and Autonomous Systems, 2022, 5: 411-444.
- 要点：权威综述，将安全学习控制（CBF、可达性、safe RL）纳入统一分类框架，为"安全保证难度递增"主线提供方法论地图。
- 支撑章节：1、5.1

**[A3]** HANOVER D, LOQUERCIO A, BAUERSFELD L, et al. Autonomous drone racing: a survey[J]. IEEE Transactions on Robotics, 2024, 40: 3044-3067.
- 要点：总结无人机竞速中模型最优控制与学习方法竞合的最新格局，是"模型—数据融合"主线的代表性近景综述。
- 支撑章节：1、3.2、4.4

**[A4]** RAZZAGHI P, TABRIZIAN A, GUO W, et al. A survey on reinforcement learning in aviation applications[J]. Engineering Applications of Artificial Intelligence, 2024, 136: 108911.
- 要点：梳理强化学习在航空（含飞行控制、决策）中的应用版图与技术缺口。
- 支撑章节：1、3.2、7

---

## 2 模型驱动方法

### 2.1 PID 与增益调度

**[B1]** ZIEGLER J G, NICHOLS N B. Optimum settings for automatic controllers[J]. Transactions of the ASME, 1942, 64: 759-768.
- 要点：PID 整定的奠基性文献，临界增益/临界周期整定法则沿用至今，是模型依赖控制的起点。
- 支撑章节：2.1、1

**[B2]** RUGH W J, SHAMMA J S. Research on gain scheduling in automatic control（自动控制中的增益调度研究）[J]. Automatica, 2000, 36(10). ※卷期页待核
- 要点：增益调度的经典综述，界定了传统/准 LPV 增益调度的理论框架及其在飞控中的早期应用。
- 支撑章节：2.1、2.4

**[B3]** LOPEZ-SANCHEZ I, MORENO-VALENZUELA J. PID control of quadrotor UAVs: a survey[J]. Annual Reviews in Control, 2023. ※卷期页待核
- 要点：四旋翼 PID 控制（含增益调度 PID）的近期系统综述，反映经典方法在小型飞行器上的持续生命力。
- 支撑章节：2.1

### 2.2 非线性动态逆（NDI / 反馈线性化）

**[C1]** LANE S H, STENGEL R F. Flight control design using nonlinear inverse dynamics[C]//Proceedings of the American Control Conference. IEEE, 1986: 587-596.
- 要点：将非线性逆动力学引入飞控设计的早期代表作，奠定 NDI 路线。
- 支撑章节：2.2

**[C2]** REINER J, BALAS G J, GARRARD W L. Flight control design using robust dynamic inversion and time-scale separation[J]. Automatica, 1996, 32(11). ※页码待核
- 要点：反馈线性化与 μ 综合结合，用时标分离实现无需调度的鲁棒动态逆飞控。
- 支撑章节：2.2、2.4

**[C3]** SIEBERLING S, CHU Q P, MULDER J A. Robust flight control using incremental nonlinear dynamic inversion and angular acceleration prediction[J]. Journal of Guidance, Control, and Dynamics, 2010, 33(6): 1732-1742.
- 要点：提出增量动态逆（INDI），通过角加速度反馈显著降低对模型失配的敏感性，成为近年 NDI 主流形态。
- 支撑章节：2.2、2.5

**[C4]** CAVERLY R J, GIRARD A R, KOLMANOVSKY I V, et al. Nonlinear dynamic inversion of a flexible aircraft[C]//IFAC-PapersOnLine, 2016, 49(17): 338-342.
- 要点：将 I/O 反馈线性化动态逆拓展到柔性飞机姿态与空速控制，并考虑输入饱和的工程可实现性。
- 支撑章节：2.2

**[C5]** ALAM M, CELIKOVSKY S. On the internal stability of non-linear dynamic inversion: application to flight control[J]. IET Control Theory & Applications, 2017, 11(12). ※页码待核
- 要点：针对 NDI 内动态稳定性给出系统分析，并提出三种纵向控制器的切换组合方案（含失速改出）。
- 支撑章节：2.2、5.3

**[C6]** SMEUR E J J, CHU Q P, DE CROON G C H E. Adaptive incremental nonlinear dynamic inversion for attitude control of micro air vehicles[J]. Journal of Guidance, Control, and Dynamics, 2016, 39(3): 450-461.
- 要点：自适应与 INDI 结合的微小型飞行器姿态控制，是模型驱动向自适应增强过渡的枢纽工作。
- 支撑章节：2.2、4.2

**[C7]** WANG X, VAN KAMPEN E J, CHU Q P, et al. Stability analysis for incremental nonlinear dynamic inversion control[J]. Journal of Guidance, Control, and Dynamics, 2019, 42(5): 1116-1129.
- 要点：给出 INDI 闭环稳定性的一般性证明，弥补其"近似鲁棒"缺乏严格论证的短板。
- 支撑章节：2.2、5.3

**[C8]** TAL E, KARAMAN S. Accurate tracking of aggressive quadrotor trajectories using incremental nonlinear dynamic inversion and differential flatness[J]. IEEE Transactions on Control Systems Technology, 2020, 29(3): 1203-1218.
- 要点：微分平坦轨迹与 INDI 结合实现激进的四旋翼轨迹跟踪，代表 NDI 在敏捷飞行中的当前水平。
- 支撑章节：2.2、6

**[C9]** GRONDMAN F, LOOYE G, KUCHAR R, et al. Design and flight testing of incremental nonlinear dynamic inversion-based control laws for a passenger aircraft[C]//2018 AIAA Guidance, Navigation, and Control Conference. AIAA, 2018.
- 要点：INDI 在载人客机上的设计—飞行试验全流程验证，是 NDI 走向工程型号的标志性案例。
- 支撑章节：2.2、6

### 2.3 自适应控制（MRAC / L1）

**[D1]** HOVAKIMYAN N, CAO C. L1 Adaptive Control Theory: Guaranteed Robustness with Fast Adaptation[M]. Philadelphia: SIAM, 2010.
- 要点：L1 自适应控制理论专著，通过滤波结构解耦"自适应速度"与"鲁棒性"，其预期首要应用即自适应飞控。
- 支撑章节：2.3

**[D2]** SONNEVELDT L, CHU Q P, MULDER J A. Nonlinear flight control design using constrained adaptive backstepping[J]. Journal of Guidance, Control, and Dynamics, 2007, 30(2): 322-336.
- 要点：约束自适应反步法用于 F-16 全包线非线性轨迹控制，代表模型驱动框架下处理不确定性的严格设计。
- 支撑章节：2.3

### 2.4 鲁棒控制（H∞ / LPV）

**[E1]** LU B, WU F, KIM S. Switching LPV control of an F-16 aircraft via controller state reset[J]. IEEE Transactions on Control Systems Technology, 2006, 14(2): 267-277.
- 要点：切换 LPV 控制器及其状态重置机制在 F-16 纵向控制中的实现，解决多凸域切换的稳定性问题。
- 支撑章节：2.4

**[E2]** SANTOSO F, LIU M, EGAN G. H2 and H∞ robust autopilot synthesis for longitudinal flight of a special unmanned aerial vehicle: a comparative study[J]. IET Control Theory & Applications, 2008, 2(7): 583-594.
- 要点：H2/H∞ 鲁棒自动驾驶仪综合的对比研究，量化鲁棒方法在 UAV 纵向控制中的性能-鲁棒折中。
- 支撑章节：2.4

**[E3]** SPARKS A G. A gain-scheduled H∞ control law for a tailless aircraft[C]//Proceedings of the IEEE International Conference on Control Applications. IEEE, 1998.
- 要点：基于高保真 LPV 模型的自动增益调度 H∞ 飞控律设计，展示 LPV-H∞ 在战斗机上的可扩展性。
- 支撑章节：2.4、2.1

**[E4]** HE T, AL-JIBOORY A K, ZHU G G, et al. Application of ICC LPV control to a blended-wing-body airplane with guaranteed H∞ performance[J]. Aerospace Science and Technology, 2018, 81: 88（起页）. DOI: 10.1016/j.ast.2018.07.046. ※尾页待核
- 要点：输入协方差约束+H∞ 的 LPV 增益调度控制用于翼身融合布局飞机的颤振/振动抑制。
- 支撑章节：2.4

### 2.5 小结
（无独立文献；由 B2、C3、C6、C7、E1 综合支撑"模型依赖递减、维护成本递增"的论断。）

---

## 3 数据驱动方法

### 3.1 神经网络飞行控制

**[F1]** EMAMI S A, CASTALDI P, BANAZADEH A. Neural network-based flight control systems: present and future[J]. Annual Reviews in Control, 2022, 53: 97-137.
- 要点：神经网络智能飞控（反馈误差学习、伪控制、神经反步、RL 自适应最优控制）的最全面数学化综述。
- 支撑章节：3.1、1

**[F2]** YANG Q, ZHANG F, WANG C. Deterministic learning-based neural PID control for nonlinear robotic systems[J]. IEEE/CAA Journal of Automatica Sinica, 2024, 11(5): 1227-1238.
- 要点：确定性学习框架下的神经 PID：闭环中学习不确定性并支持知识复用，体现"学习增强经典控制"的思想。
- 支撑章节：3.1、4.2

**[F3]** KAUFMANN E, BAUERSFELD L, LOQUERCIO A, et al. Champion-level drone racing using deep reinforcement learning[J]. Nature, 2023, 620(7976): 982-987.
- 要点：Swift 系统以仿真深度强化学习+真实世界数据在物理竞速中战胜人类世界冠军，是数据驱动飞控的里程碑。
- 支撑章节：3.2、3.1、6

**[F4]** SONG Y, ROMERO A, MÜLLER M, et al. Reaching the limit in autonomous racing: optimal control versus reinforcement learning[J]. Science Robotics, 2023, 8(82): eadg1462.
- 要点：以时间最优竞速为基准直接对比最优控制与强化学习，揭示两类方法在极限性能与鲁棒性上的互补。
- 支撑章节：3.1、3.2、4.4

### 3.2 强化学习飞行控制

**[G1]** LOQUERCIO A, KAUFMANN E, RANFTL R, et al. Learning high-speed flight in the wild[J]. Science Robotics, 2021, 6(59): eabg5810.
- 要点：特权学习+端到端策略实现复杂野外环境高速避障飞行，零样本 sim-to-real。
- 支撑章节：3.2

**[G2]** KAUFMANN E, LOQUERCIO A, RANFTL R, et al. Deep drone racing: learning agile flight in dynamic environments[C]//Proceedings of the 2nd Conference on Robot Learning (CoRL), PMLR, 2018, 87: 133-145.
- 要点：CNN 将图像直接映射为航点与期望速度的敏捷飞行框架，无需显式环境地图。
- 支撑章节：3.2

**[G3]** KAUFMANN E, GEHRIG M, FOEHN P, et al. Beauty and the beast: optimal methods meet learning for drone racing[C]//2019 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2019: 690-696.
- 要点：最优控制与学习方法混合（学习估计+模型规划），获 IROS 2018 自主竞速赛冠军。
- 支撑章节：3.2、4.4

**[G4]** LOQUERCIO A, KAUFMANN E, RANFTL R, et al. Deep drone racing: from simulation to reality with domain randomization[J]. IEEE Transactions on Robotics, 2020, 36(1): 1-14.
- 要点：域随机化实现敏捷飞行的首个零样本 sim-to-real 迁移，是"仿真到现实"方法论的代表作。
- 支撑章节：3.2

**[G5]** SONG Y, STEINWEG M, KAUFMANN E, et al. Autonomous drone racing with deep reinforcement learning[J]. IEEE Robotics and Automation Letters, 2021, 6(2): 7563-7570. ※页码待核（另见 IROS 2021: 1205-1212 会议版本）
- 要点：闭环深度 RL 完成时间最优竞速全流程（感知-规划-控制一体训练）。
- 支撑章节：3.2

**[G6]** KAUFMANN E, BAUERSFELD L, SCARAMUZZA D. A benchmark comparison of learned control policies for agile quadrotor flight[C]//2022 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2022: 10504-10510.
- 要点：首个学习型控制策略动作空间基准：推力+体速率抽象最利于 sim-to-real，45 km/h 真机验证。
- 支撑章节：3.2、5.3

**[G7]** CHEN S, LI Y, LOU Y, et al. Aggressive and robust low-level control and trajectory tracking for quadrotors with deep reinforcement learning[J]. Neural Computing and Applications, 2024, 37(3): 1223-1240.
- 要点：RL 低层控制策略在扰动环境中精度与鲁棒性超越 PID，并完成 6 m/s 真机轨迹跟踪。
- 支撑章节：3.2

**[G8]** KIM J, JUNG S. Enhancing UAV stability: a deep reinforcement learning strategy[C]//2024 International Conference on Electronics, Information, and Communication (ICEIC). IEEE, 2024. DOI: 10.1109/ICEIC61013.2024.10457214.
- 要点：DDPG 姿态控制较 PID 具有更快的干扰恢复能力，展示 DRL 在小型 UAV 上的工程可行性。
- 支撑章节：3.2

**[G9]** DO T, MUNG N X, HONG N X, et al. Deep reinforcement learning-based quadcopter controller: a practical approach and experiments[EB/OL]. arXiv:2406.08815, 2024.
- 要点：端到端 RL 控制器免微调部署至 Crazyflie 真机，数据高效且免人工调参。
- 支撑章节：3.2

**[G10]** 章胜, 周攀, 何扬, 等. 基于深度强化学习的空战机动决策试验[J]. 航空学报, 2023, 44(10): 128094.
- 要点：深度强化学习机动决策与飞控律跟踪结合，完成人机对抗飞行试验，实现智能决策从仿真到真实飞行的迁移（中文核心）。
- 支撑章节：3.2、6

**[G11]** 蔡云鹏, 周大鹏, 丁江川. 具有防撞安全约束的无人机集群智能协同控制[J]. 航空学报, 2024, 45(5): 529683.
- 要点：DRL 与防撞策略结合的集群协同控制，兼顾紧密一致性与安全性（中文核心）。
- 支撑章节：3.2、5.1

**[G12]** ZHAO H, FU H, YANG F, et al. Data-driven offline reinforcement learning approach for quadrotor's motion and path planning[J]. Chinese Journal of Aeronautics, 2024, 37(11): 386-397.
- 要点：离线 RL 避免真机交互风险，悲观估计保证策略保守性，适合工业级 UAV 场景。
- 支撑章节：3.2、5.1

**[G13]** FOEHN P, BRESCIANINI D, KAUFMANN E, et al. AlphaPilot: autonomous drone racing[J]. Autonomous Robots, 2022, 46(1): 307-320.
- 要点：大型自主竞速系统（2019 AlphaPilot 竞赛冠军）的完整技术报告，覆盖感知-规划-控制闭环。
- 支撑章节：3.2、6

### 3.3 数据驱动辨识与状态估计

**[H1]** AHMED S, AMER A, VARELA C A, et al. Data-driven state awareness for fly-by-feel aerial vehicles via adaptive time series and Gaussian process regression models[C]//Dynamic Data Driven Applications Systems (DDDAS) Workshop, 2020.
- 要点：自适应时序模型与高斯过程回归实现自感知机翼的飞行状态感知，代表"fly-by-feel"数据驱动估计路线。
- 支撑章节：3.3

**[H2]** WILD G. AI-based flight state identification for aerospace control systems[C]//AIAA Paper 2026-4755. AIAA, 2026. DOI: 10.2514/6.2026-4755.
- 要点：无监督聚类（GMM/ART/PAM）从真实飞行数据中辨识飞行状态，无需标注与预设模型结构。
- 支撑章节：3.3

**[H3]** BAUERSFELD L, KAUFMANN E, FOEHN P, et al. NeuroBEM: hybrid aerodynamic quadrotor model[C]//Robotics: Science and Systems (RSS), 2021.
- 要点：神经网络混合气动模型显著超越一阶/二阶气动模型，为模型驱动控制提供数据驱动的"模型即插件"。
- 支撑章节：3.3、4.1

### 3.4 小结
（由 F4、G6、H3 支撑"数据利用度递增"论断。）

---

## 4 融合架构

### 4.1 残差学习控制

**[I1]** O'CONNELL M, SHI G, SHI X, et al. Neural-Fly enables rapid learning for agile flight in strong winds[J]. Science Robotics, 2022, 7(66): eabm6597.
- 要点：离线元学习气动残差基函数+在线 MRAC 复合自适应律，仅 12 分钟数据即可在强风中精确跟踪，具备李雅普诺夫稳定性保证。
- 支撑章节：4.1、3.1

**[I2]** SHI G, SHI X, O'CONNELL M, et al. Neural lander: stable drone landing control using learned dynamics[C]//2019 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2019.
- 要点：以神经网络学习地面效应残差气动并嵌入反馈控制，实现带稳定性保证的稳定着陆。
- 支撑章节：4.1

### 4.2 学习增强自适应/鲁棒控制

**[J1]** HOVAKIMYAN N, CAO C, KHARISOV E, et al. L1 adaptive control for safety-critical systems[J]. IEEE Control Systems Magazine, 2011, 31(5). ※页码待核
- 要点：L1 自适应飞控在 NASA AirSTAR/GTM 上的设计与飞行试验综述：唯一在失速/深失速区完成全部试验卡的自适应控制器。
- 支撑章节：4.2、2.3、6

**[J2]** ACKERMAN K A. L1 adaptive control flight testing and extension to nonlinear reference systems with unmatched uncertainty[D]. Urbana-Champaign: University of Illinois at Urbana-Champaign, 2021.
- 要点：L1 从无人平台到载人 Learjet 飞行试验的推进，并扩展至非匹配不确定性非线性参考系统（面向 UAM 倾转构型）。
- 支撑章节：4.2、6

### 4.3 学习增强 MPC 与最优控制

**[K1]** ROSOLIA U, BORRELLI F. Learning model predictive control for iterative tasks: a data-driven control framework[J]. IEEE Transactions on Automatic Control, 2017, 63(7): 1883-1896.
- 要点：迭代任务下的学习 MPC 框架，从历史数据在线收敛到约束可行闭环，奠基"数据驱动 MPC"方向。
- 支撑章节：4.3

**[K2]** KORDABAD A B, REINHARDT D, ANAND A S, et al. Reinforcement learning for MPC: fundamentals and current challenges[C]//IFAC-PapersOnLine, 2023, 56(2): 5773-5780.
- 要点：RL-MPC 结合的统一理论图景（稳定性与安全性保证）及开放挑战综述。
- 支撑章节：4.3

**[K3]** TORRENTE G, KAUFMANN E, FOEHN P, et al. Data-driven MPC for quadrotors[J]. IEEE Robotics and Automation Letters, 2021, 6(2): 3769-3776.
- 要点：以学习型预测模型（含阻尼/拖曳效应）替换解析模型嵌入 MPC，实现高速轨迹跟踪。
- 支撑章节：4.3

**[K4]** SUN S, ROMERO A, FOEHN P, et al. A comparative study of nonlinear MPC and differential-flatness-based control for quadrotor agile flight[J]. IEEE Transactions on Robotics, 2022, 38(6): 3357-3373.
- 要点：NMPC 与微分平坦控制的全面对比实证，为敏捷飞行控制器选型提供定量基准。
- 支撑章节：4.3

**[K5]** ROMERO A, SUN S, FOEHN P, et al. Model predictive contouring control for time-optimal quadrotor flight[J]. IEEE Transactions on Robotics, 2022, 38(6): 3340-3356.
- 要点：将全局时间最优轨迹与实时 MPCC 控制结合，突破"规划-控制"分离的性能瓶颈。
- 支撑章节：4.3

**[K6]** HANOVER D, FOEHN P, SUN S, et al. Performance, precision, and payloads: adaptive nonlinear MPC for quadrotors[J]. IEEE Robotics and Automation Letters, 2021, 7(2): 690-697.
- 要点：自适应 NMPC 在载荷/气动参数变化下保持高精度跟踪，体现模型-数据混合的鲁棒化路线。
- 支撑章节：4.3

**[K7]** FOEHN P, ROMERO A, SCARAMUZZA D. Time-optimal planning for quadrotor waypoint flight[J]. Science Robotics, 2021, 6(56): eabh1221.
- 要点：多航点时间最优轨迹规划的可证最优求解，为"性能上限"提供模型驱动基准。
- 支撑章节：4.3、3.2

**[K8]** KRINNER M, ROMERO A, BAUERSFELD L, et al. MPCC++: model predictive contouring control for time-optimal flight with safety constraints[C]//Robotics: Science and Systems (RSS), 2024.
- 要点：在 MPCC 中显式引入安全约束，实现带保证的时间最优飞行，是"性能+安全"融合的近期代表作。
- 支撑章节：4.3、5.1

**[K9]** SEEL K. Learning for model predictive control[D]. Trondheim: Norwegian University of Science and Technology (NTNU), 2023.
- 要点：系统研究学习模型与 RL 参数化 MPC 的稳定性/鲁棒性保证，覆盖监督学习与 RL 两条融合路径。
- 支撑章节：4.3

**[K10]** 李苑, 刘双喜, 杜兆波, 等. 模型预测控制及其在飞行器系统中的应用综述[J]. 国防科技大学学报, 2026, 48(2): 144-162.
- 要点：面向四旋翼、直升机、固定翼与高速飞行器的 MPC 框架体系与应用综述（中文核心，含鲁棒/Lyapunov/切换/显式 MPC 脉络）。
- 支撑章节：4.3、1

### 4.4 小结
（由 F4、G3、K4、K5 支撑"模型与数据互补融合"论断。）

---

## 5 安全保证与可信性

### 5.1 CBF 与可达性 / 安全强化学习

**[L1]** GARCÍA J, FERNÁNDEZ F. A comprehensive survey on safe reinforcement learning[J]. Journal of Machine Learning Research, 2015, 16: 1437-1480.
- 要点：安全强化学习奠基综述，确立"修正最优准则"与"修正探索过程"两大范式。
- 支撑章节：5.1

**[L2]** HERBERT S L, CHOI J J, QAZI S, et al. Scalable learning of safety guarantees for autonomous systems using Hamilton-Jacobi reachability[C]//2021 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2021.
- 要点：分解+热启动+自适应网格使 HJ 可达性安全分析可在线更新（10 维四旋翼风场演示），打通"安全集学习更新"路径。
- 支撑章节：5.1

**[L3]** PANJA P, HOAGG J B, BAIDYA S. Control barrier function based UAV safety controller in autonomous airborne tracking and following systems[EB/OL]. arXiv:2312.17215, 2023.
- 要点：CBF-QP 安全滤波器可对接任意商用自驾仪，以最小干预保证目标追踪/避撞安全。
- 支撑章节：5.1

**[L4]** SOLANKI P, EL-HAJJ I, VAN BEERS J, et al. Unifying Hamilton-Jacobi reachability and reinforcement learning[EB/OL]. arXiv:2601.08050, 2026.
- 要点：给出 HJ 可达性与 RL 的统一运行代价表述并证明粘性解收敛，为学习型安全滤波提供理论根基。
- 支撑章节：5.1

**[L5]** MANNUCCI T, VAN KAMPEN E J, DE VISSER C, et al. Safe exploration algorithms for reinforcement learning controllers[J]. IEEE Transactions on Neural Networks and Learning Systems, 2017, 29(4): 1069-1081.
- 要点：面向飞控 RL 的安全探索算法，在未知环境学习中保证不违反安全约束。
- 支撑章节：5.1、3.2

### 5.2 神经网络验证与认证

**[M1]** KATZ G, BARRETT C, DILL D L, et al. Reluplex: an efficient SMT solver for verifying deep neural networks[C]//Computer Aided Verification (CAV). Cham: Springer, 2017. ※页码待核
- 要点：首个可扩展验证 ReLU 网络性质的 SMT 求解器，并以 ACAS Xu 空中防撞系统为基准，成为航空神经网络安全验证的基石。
- 支撑章节：5.2

**[M2]** JULIAN K D, KOCHENDERFER M J. Guaranteeing safety for neural network-based aircraft collision avoidance systems[EB/OL]. arXiv:1912.07084, 2019.
- 要点：将神经网络验证工具与可达性分析结合，为 ACAS Xu 类神经网络防撞逻辑提供安全证明路径。
- 支撑章节：5.2

**[M3]** DMITRIEV K, SCHUMANN J, HOLZAPFEL F. Toward design assurance of machine-learning airborne systems[C]//AIAA, 2021. (NASA NTRS 20210025705)
- 要点：以跑道标志识别 DNN 为案例，讨论 ML 机载系统在 DO-178C 体系下的设计保证目标与 DAL 分级差距。
- 支撑章节：5.2

**[M4]** EUROPEAN UNION AVIATION SAFETY AGENCY (EASA), DAEDALEAN. Concepts of design assurance for neural networks (CoDANN)[R]. Cologne: EASA, 2020.
- 要点：监管机构与工业界联合提出的神经网络"学习保证"W 型流程，是 AI 适航认证体系的关键构建块。
- 支撑章节：5.2、7

**[M5]** ASTM INTERNATIONAL. ASTM F3269-21: Standard practice for methods to safely bound behavior of aircraft systems containing complex functions using run-time assurance[S]. West Conshohocken: ASTM International, 2021.
- 要点：运行时保证（RTA）架构标准，为含 ML/RL 的复杂飞控功能提供"设计时保证"之外的认证合规路径。
- 支撑章节：5.2、5.1、7

### 5.3 再审视
（由 C5、C7、L2、M1、M5 综合支撑"数据驱动方法安全保证难度递增、需形式化+运行时双重防线"的论断；另见 G6 关于 sim-to-real 中动作空间抽象的讨论。）

---

## 6 工程应用进展

**[N1]** FOEHN P, KAUFMANN E, ROMERO A, et al. Agilicious: open-source and open-hardware agile quadrotor for vision-based flight[J]. Science Robotics, 2022, 7(67): eabl6259.
- 要点：开源开放硬件敏捷四旋翼平台，同时支持模型控制与学习策略快速部署，降低飞控算法飞行验证门槛。
- 支撑章节：6

**[N2]** SPENCER C T. Development, modeling, identification, and control of tilt-rotor eVTOL aircraft[D]. Logan: Utah State University, 2024.
- 要点：倾转旋翼 eVTOL 的建模—最小二乘辨识—PID 增益优化完整流程，代表 eVTOL 过渡飞行控制的工程实践。
- 支撑章节：6、3.3

**[N3]** 邓景辉. 高速直升机关键技术与发展[J]. 航空学报, 2024, 45(9): 529085.
- 要点：高速直升机（复合/共轴刚性旋翼）构型与飞控关键技术展望，反映高机动旋翼飞行器工程前沿（中文核心）。
- 支撑章节：6

**[N4]** 吴希明, 吕乐丰, 张广林. 民用高速旋翼飞行器发展战略分析及关键技术展望[J]. 南京航空航天大学学报, 2022, 54(5): 827-835.
- 要点：民用高速旋翼飞行器的战略分析与关键技术（含飞控/振动/动力学）路线图（中文核心）。
- 支撑章节：6

（补充说明：亿航 EH216-S 取得全球首张无人驾驶载人 eVTOL 型号合格证、峰飞"盛世龙"完成 eVTOL 跨海跨城首飞、沃飞长空 AE200 完成全包线倾转过渡试飞等 2023—2024 年工程里程碑均有公开报道，但属新闻/取证事件而非学术文献，建议在正文 6 章以事实性叙述引用并注明新闻来源，不计入参考文献总数。）

---

## 统计表

| 类别 | 数量 | 说明 |
|---|---|---|
| 期刊论文 [J] | 42 | 含 8 篇综述（A1、A2、A3、A4、B3、F1、L1、K10） |
| 会议论文 [C] | 18 | 含 ICRA/IROS/RSS/CoRL/CAV/ACC/AIAA/DDDAS/ICEIC/IEEE-CAA 等 |
| 预印本 [EB/OL] | 4 | arXiv（G9、L3、L4、M2），建议终稿核对是否已有正式版本 |
| 专著 [M] / 学位论文 [D] | 4 | SIAM 专著 1 + 博士/硕士学位论文 3（J2、K9、N2） |
| 标准/技术报告 [S/R] | 2 | ASTM F3269-21、EASA CoDANN |
| **总计** | **70** | 满足 65–75 条目标 |

**年份分布**
- 2021–2026 年文献：40 条（占 57%）
- 2020 年及以前经典/重要文献：30 条（含 Ziegler–Nichols 1942、Lane–Stengel 1986、Reiner 1996、Rugh–Shamma 2000、Sieberling 2010 等奠基文献）

**主题覆盖核验**（15 个规定主题 → 文献组）
1. PID/增益调度 → B1–B3；2. NDI/反馈线性化 → C1–C9；3. MRAC/L1 → D1–D2、J1–J2；4. H∞/LPV → E1–E4、C2；5. 神经网络控制 → F1–F2、I1–I2；6. RL 飞行控制 → G1–G13；7. sim-to-real/敏捷飞行 → G1、G4、G6、F3、F4；8. 数据驱动辨识估计 → H1–H3、H3；9. 残差学习/NN 增强 → I1–I2、H3；10. 学习增强 MPC → K1–K10；11. CBF/安全滤波 → L3、M5；12. HJ 可达性/safe RL → L1、L2、L4、L5；13. NN 验证认证 → M1–M5；14. 飞控综述 → A1–A4、B3、F1、K10；15. 高机动/eVTOL/多旋翼飞行试验 → C9、G10、N1–N4。

**真实性核验方式说明**
1. 全部条目来自 WebSearch 返回结果中实际出现的信息，标题、作者、载体、年份经≥1 个独立来源（出版社页、ADS、Google Scholar、arXiv、期刊官网或权威二手参考文献列表）确认；
2. 关键条目（Ziegler–Nichols 1942、Sieberling 2010、Swift/Nature 2023、Neural-Fly/Sci Robotics 2022、Reluplex/CAV 2017、ASTM F3269-21 等）经出版社或学会官方页面直接确认；
3. 带"※"的条目仅个别字段（卷期页）未在检索结果中完整给出，已在条目内标注"待核"，终稿前应以 DOI/Crossref 复核；
4. 检索中发现但信息不足以确认作者/卷期的候选条目（如可解释机器学习辨识、NN-MPC RA-L 2022、多篇会议摘要）已按"存疑即弃"原则排除，未计入。
