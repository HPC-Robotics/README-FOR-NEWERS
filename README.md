# Welcome to HPC Robotics Lab!
# 1. Startup
hello! 作为一个刚接触RL robotics的小白，你一定有很多的疑问，不知道从哪里开始学习，下面是我们HPC老登整理好的一个pipeline，帮助你循序渐进的知道自己需要了解什么，该学什么，该干什么。俗话说兴趣是最好的老师，如果遇到困难也请不要放弃，多问问老师学长，总会突破的！（笔者入门是想复刻迪士尼机器人研究院的BD-X机器人才一步步攻坚克难的）

最好提前掌握的知识和技能（当然不会也没事儿，后面都能学会）：
- 深度学习基础
- 机器人学原理
- Python，Pytorch
- 了解ROS咋个通信方式等

## 1.1 为什么使用RL？
传统方法使用MPC进行base位置以及落足点位置的预测，然后使用WBC的方法将位置信号转化为各个关节的角度信息，而采用强化学习的方法是一种端到端的动作输出范式，通过深度神经网络实现从观测输入到电机角度直接输出的统一范式。另外，对于一些非结构化的场景，传统控制往往依赖大量的计算优化和泛化性调优，这就导致机器人可能在平地上走的很好，但是一到楼梯地形就频繁摔倒等；而采用RL的方法往往通过“黑盒”的适应性训练，提高了对多数场景的泛化能力，不仅见过的地形可以走的很好，没见过的场景（OOD, **O**ut **O**f **D**istribution）依然可以实现稳定适应。
<img width="1212" height="516" alt="image" src="https://github.com/user-attachments/assets/69c797fa-eda1-4541-b370-9c2b7c93f58c" />

## 1.2 为什么大部分论文与项目都使用Isaac仿真？
Isaac 是什么见 [第 3 节 仿真环境](#section-3)。Isaac是英伟达开发的仿真环境，并行性好，收集数据效率高。在以Isaac为代表的GPU并行环境之前，大多使用CPU集群运行并行环境，例如habitat等，训练效率等不高。
<img width="1049" height="453" alt="image" src="https://github.com/user-attachments/assets/7e525fef-604c-4433-acd0-34fa39c230b9" />
而基于Isaac的项目Legged gym贡献：
1. 提供成熟的四足机器人封装，以及地形的生成代码

2. Physx引擎进行环境的Reset或者update速度很慢，legged gym创建一个巨大的地形，并按照行列划分，在行维度上生成不同类型的地形，在列维度上生成不同的难度

3. 对于不同的并行四足机器人环境来说，地形是共享的，但是不同环境之间并不会发生影响或碰撞。采用难度递增式的地形课程学习来逐步提升训练质量。
<img width="970" height="547" alt="image" src="https://github.com/user-attachments/assets/7e9e46a1-42a5-4328-9094-4009d40f36bd" />


# 2. 强化学习理论
鲁迅说过：要用理论指导实践。一上来就做项目复现肯定是一头雾水的，所以需要先打好理论上的基础，知道什么是强化学习，怎么用强化学习去实现机器人的控制等等。对于理论知识部分，这里最推荐b站上西湖大学赵世钰老师的[链接-强化学习课程](https://www.bilibili.com/video/BV1sd4y167NS/?spm_id_from=333.337.search-card.all.click&vd_source=dbf0a5b21ee3e608136d2de2f7aa4035)

希望看视频的时候一定要拿出本认真推导和记录里面的知识点，但是赵老师的课就到Actor-Critic基本就结束了，但是相信看完了后你自己也有能力去互联网上搜索和学习比如TRPO、PPO、PPO2等主流算法，这里贴一个笔者的早期学PPO时记录的博客：[链接-PPO算法](https://www.cnblogs.com/myleaf/p/18595876)
写的比较详细，希望可以帮助到你。

<a id="section-3"></a>
# 3. 仿真环境
训练的仿真环境主要是Nvidia提供的两个，Isaac Gym和Isaac Sim。前者是最早推出的并行仿真训练环境，也是早期各种RL工作的基础，而后者相当于前者的一次迭代，将更多的函数实现封装成供用户灵活调用的API接口（如果你做过嵌入式设计，就会熟悉sim32的标准库以及CUBEMX的区别，这两个也类似），比如奖励函数的实现、雷达信息的调用等等。目前主流的使用一般是Isaac Lab，但是它上手一般来说会更难一些，所以作为初学者先从Isaac Gym开始。下面我会推荐几个比较适合入门的项目。

Isaac Gym安装包: [链接-Isaac Gym install](https://developer.nvidia.com/isaac-gym)
具体安装过程可参考[链接-具体安装](https://github.com/leggedrobotics/legged_gym?tab=readme-ov-file)

Isaac Lab中文文档: [链接-Isaac Lab install](https://docs.robotsfan.com/isaaclab/index.html)
具体安装过程可参考“上述文档”

# 4. 理论实践项目
鲁迅还说过：实践是检验真理的唯一标准。所以是时候检验下自己是否真的掌握了RL的理论知识了。这里主要推荐最经典也是最基础的工程项目就是[链接-legged gym](https://github.com/leggedrobotics/legged_gym)
这个是基于Isaac Gym实现的对四足机器人的兼容框架，由苏黎世联邦理工大学和Nvidia联合开源（ETH，后面会反复提到这所在Robotics Engineering领域国际顶尖的学校），这个训练框架是目前所有框架的重要基础，只有真正弄懂了里面的代码逻辑和工程实现才算真正入门了这个方向。

还有一个同样是ETH的RSL_RL库（**R**obotics **S**ystem **L**ab _ **R**einforcement **L**earning）：[链接-rsl_rl](https://github.com/leggedrobotics/rsl_rl)
这个库是对PPO的代码的具体实现，这里我们主要关注下面的几个文件即可：
```
rsl_rl/
├── algorithms/        %这个文件是PPO算法的实现部分，包括损失函数、actor critic的mlp的更新逻辑，包含了models的网络初始化
├── env/               %这个是环境env.py的封装，为算法库提供了比如step、get_observations等接口的调用
├── extensions/        %一些拓展，比如镜像设置、随机网络蒸馏等（雅加达游戏）
├── models/            %这个是模块的调用
├── modules/           %一些比如mlp、cnn等模块的代码实现
├── runners/on_policy_runner.py %主要在train.py中调用的函数文件，相当于是一个main.py，里面集合了algorithms、modules等所有的实现
├── storage/           %进行环境rollout和代码，定义了replay buffer
├── utils/             %一些工具
└── __init__.py
```

这里最好一定要对照着赵老师的强化学习课程和这个RSL-RL代码进行学习，这样才会明白每一步都具体是（What）怎么干什么的，（Why）为什么这么做以及（How）如何实现的。

# 5. 主流任务
目前主流的机器人有四足机器人、轮足机器人（包括四轮足、双轮足等）以及人形机器人等，这些机器人的RL运动控制任务主要有两类：

- 一类是locomotion的任务，也就是运动学的任务。这种任务基本有两种实现方法，一种是通过LiDAR（Light Detection And Ranging 激光雷达）、depth camera（深度相机）等设备来感知外界地形信息，比如说草丛楼梯等；另一种是不使用上述这些感知设备，也就是“无感知的盲人”，一般对于四足机器人就叫“盲狗”（其他机器人也可以代指），这种实现方法一般对于一些简单的任务比较实用，而且训练成本也比较低一些，但是对于地形更复杂的任务比如穿越雪地、草坪等，就表现的不是很好了。
  
- 另一类是loco-manipulation的任务，也就是运动加上肢控制的任务，这里上肢对于人形机器人就是字面意思，对于其他机器人一般指的是携带机械臂的操作性任务。
  
- 其实还应该有第三种，就是motion tracking的任务，这种任务对于人形机器人来说主要就是为了完成动作模仿任务，比方说跳舞、杂技等（详见2026宇树春节联欢晚会）、像人类一样的直膝行走等，而对于其他机器人主要就是各种步态的模仿，比如四足机器人希望走狼的步态、豹子的步态等等，使用的方法基本是基于AMP的动作对抗生成。

## 5.1 推荐可复现的github项目
- 四足机器人locomotion任务：[Legged Gym](https://github.com/leggedrobotics/legged_gym) 、 [Extreme Parkour](https://github.com/chengxuxin/extreme-parkour) 、 [HIM](https://github.com/InternRobotics/HIMLoco) 、 [Walk These Ways](https://github.com/Improbable-AI/walk-these-ways)
- 人形机器人动作跟踪模仿任务：[H2O](https://github.com/LeCAR-Lab/human2humanoid) 、 [BeyondMimic](https://github.com/HybridRobotics/whole_body_tracking)
- 人形机器人locomotion任务：[Humanoid Gym](https://github.com/roboterax/humanoid-gym) 

# 6. 经典论文推荐
这里为了便于了解和学习，我将一些比较经典的论文进行了整理和简单的说明，便于你们进行系统性的学习。(这些论文只是例子，当然追踪热点肯定是最好的)
## 6.1 四足机器人
- **[《Learning Quadrupedal Locomotion over Challenging Terrain》](https://arxiv.org/abs/2010.11251)** 强化学习开山之作，盲狗
- [《RMA: Rapid Motor Adaptation for Legged Robots》](https://arxiv.org/abs/2107.04034) 师生网络蒸馏，使用更长时间的历史本体观测序列，历史观测编码器推理较慢无需实时
- [《Walk These Ways: Tuning Robot Control for Generalization with Multiplicity of Behavior》](https://arxiv.org/abs/2212.03238) 早期四足机器人多步态的探索
- [《CTS: Concurrent Teacher-Student Reinforcement Learning for Legged Locomotion》](https://arxiv.org/abs/2405.10830) 两阶段并为一阶段的训练方法
- [《DreamWaQ: Learning Robust Quadrupedal Locomotion With Implicit Terrain Imagination via Deep Reinforcement Learning》](https://arxiv.org/abs/2301.10602) 使用VAE进行特权信息估计
- [《Extreme Parkour with Legged Robots》](https://arxiv.org/abs/2309.14341) 使用height scan到深度相机的师生蒸馏策略
- [《Robot Parkour Learning》](https://arxiv.org/abs/2309.05665) 使用深度相机
- [《Hybrid Internal Model: Learning Agile Legged Locomotion with Simulated Robot Response》](https://arxiv.org/abs/2312.11460) 使用对比学习替换传统的loss回归
- 《Learning Robust and Agile Legged Locomotion Using Adversarial Motion Priors》 使用AMP提供四足的步态模仿

## 6.2 （带臂）四足机器人
- [《Deep Whole-Body Control: Learning a Unified Policy for Manipulation and Locomotion》](https://arxiv.org/abs/2210.10044) 提出ROA这种师生网络交替更新的方法
- [《UMI on Legs: Making Manipulation Policies Mobile with Manipulation-Centric Whole-body Controllers》](https://arxiv.org/abs/2407.10353) 作者是UMI的提出者，用到了四足的WBC任务上
- [《Helpful DoggyBot: Open-World Object Fetching using Legged Robots and Vision-Language Models》](https://arxiv.org/abs/2410.00231) 用狗的leg或者mouth作为夹爪加持（比较fancy，不实用其实）
  
## 6.3 人形机器人 -- 动作跟踪模仿
- [《Learning Human-to-Humanoid Real-Time Whole-Body Teleoperation》](https://arxiv.org/abs/2403.04436) 何大佬的工作，人形动作模仿的开山之作
- [《OmniH2O: Universal and Dexterous Human-to-Humanoid Whole-Body Teleoperation and Learning》](https://arxiv.org/abs/2406.08858) 何大佬的工作，人形动作模仿的开山之作
- [《ASAP: Aligning Simulation and Real-World Physics for Learning Agile Humanoid Whole-Body Skills》](https://arxiv.org/abs/2502.01143) 2025年初实现科比跳投等高难度动作，采用real2sim2real的方法
- [《Retargeting Matters: General Motion Retargeting for Humanoid Motion Tracking》](https://arxiv.org/abs/2510.02252) 提供了人体重定向的方法，也就是生成机器人运动数据先验用于motion mimic的任务
- [《TWIST: Teleoperated Whole-Body Imitation System》](https://arxiv.org/abs/2505.02833) 和GMR同一个作者，方法继承实现动作跟踪
- [《Mobile-TeleVision: Predictive Motion Priors for Humanoid Whole-Body Control》](https://arxiv.org/abs/2412.07773) 将上下肢解耦控制 Exbody系列论文
- [《Expressive Whole-Body Control for Humanoid Robots》](https://arxiv.org/abs/2402.16796) Exbody系列论文
- [《ExBody2: Advanced Expressive Humanoid Whole-Body Control》](https://arxiv.org/abs/2412.13196) Exbody系列论文
- [《Learning Humanoid Standing-up Control across Diverse Postures》](https://arxiv.org/abs/2502.08378) 人形机器人分阶段起身，采用辅助力的策略比较有启发
- [《OmniRetarget: Interaction-Preserving Data Generation for Humanoid Whole-Body Loco-Manipulation and Scene Interaction》](https://arxiv.org/abs/2509.26633) 近期工作，与环境交互互动等
- [《OmniXtreme: Breaking the Generality Barrier in High-Dynamic Humanoid Control》](https://arxiv.org/abs/2602.23843) 宇树和高校联合的项目，实现机器人极端运动的跟踪和模仿，使用flow matching
- [《SONIC: Supersizing Motion Tracking for Natural Humanoid Whole-Body Control》](https://arxiv.org/abs/2511.07820) 英伟达出的人形机器人中的“通用模型框架”
- [《RobotDancing: Residual-Action Reinforcement Learning Enables Robust Long-Horizon Humanoid Motion Tracking》](https://arxiv.org/abs/2509.20717) 人形机器人出残差action辅助训练
- [《Track Any Motions under Any Disturbances》](https://arxiv.org/abs/2509.13833) 预测极端外力干扰，进行强鲁棒的跳舞任务
- [《Visual Imitation Enables Contextual Humanoid Control》](https://arxiv.org/abs/2505.03729) 从视频中学习跳舞动作
- [《CLONE: Closed-Loop Whole-Body Humanoid Teleoperation for Long-Horizon Tasks》](https://arxiv.org/abs/2506.08931) 北理工的VR动作跟踪，加里程计进行位置感知
- [《AMO: Adaptive Motion Optimization for Hyper-Dexterous Humanoid Whole-Body Control》](https://arxiv.org/abs/2505.03738) 动作跟踪，上下肢分离控制
- [《BeyondMimic: From Motion Tracking to Versatile Humanoid Control via Guided Diffusion》](https://arxiv.org/abs/2508.08241) 廖佬的开源动作跟踪框架，之前的MPC+WBC也是最初这位大佬开源的

## 6.4 人形机器人 -- 全地形运动控制
- [《Humanoid Parkour Learning》](https://arxiv.org/abs/2406.10759) 人形感知跑酷
- [《Humanoid-Gym: Reinforcement Learning for Humanoid Robot with Zero-Shot Sim2Real Transfer》](https://arxiv.org/abs/2404.05695) 比较推荐的人形机器人初学的框架，能训练出来一个机器人，也可移植自己的机器人进去
- [《Perceptive Humanoid Parkour: Chaining Dynamic Human Skills via Motion Matching》](https://arxiv.org/abs/2602.15827) 使用原子技能库实现不同地形的跑酷，比如爬墙等
- [《Deep Whole-body Parkour》](https://arxiv.org/abs/2601.07701) 庄子文大佬的全身跑酷系列
- [《Attention-Based Map Encoding for Learning Generalized Legged Locomotion》](https://arxiv.org/abs/2506.09588) 使用注意力机制学习最优落足点
- [《BeamDojo: Learning Agile Humanoid Locomotion on Sparse Footholds》](https://arxiv.org/abs/2502.10363) 梅花桩行走的两阶段迁移任务，从平坦地形到复杂地形


## 6.5 VLA相关
我们采用自顶向下的方法来拆解技术，
首先VLA比较简单，实际上你只需要看一篇文章：[pi0论文🔗🔗🔗](https://arxiv.org/html/2410.24164v1) 就已经掌握这个领域的50%了
### 6.5.1连续动作表示的VLA
#### pi0
这是现代VLA网络的开山鼻祖，后续pi05 pi06 pi07都是和pi0差不多的网络架构，也就是说pi0基本就相当于确定了VLA的整个网络框架（VLM主干+Flow expert专家），为行业写了一份白皮书
<img width="1132" height="532" alt="image" src="https://github.com/user-attachments/assets/b9e8e4e4-06e6-44f0-938a-0a516b3fa3d7" />

##### pi0分为左右两部分：
- 左边是pre-train的VLM，训练范式方面，采用regression（回归）训练，pi0和pi05用的就是paligemma1的VLM，实现中使用双向注意力；右边是action expert，是flow-matching训练的diffusion transformer（DiT）
- 右边是post-train的expert，训练范式方面，采用Flow Matching（流匹配）训练，注意它不回归动作，而是一个指向动作的速度向量，连续对t的0->1（openpi实现中是1->0）欧拉积分下会“流到”目标动作。

当然咱们训练不用管什么pre/post-train，也不用管KI[knowledge insulation](https://arxiv.org/abs/2505.23705),因为咱们的算力不可能做pre-train，当然KI论文本身值得一看，而实际我们HPC自己可做的肯定只有后训练（预训练那是企业要做的事，比如阿里的lingbot就在尝试做，虽然做的目前还是依托答辩），我们自己是vlm+action一起做训练，不stop-gradient搞KI，也不对action expert单独训练（效果会很差），也不做LoRA训练，同样是效果很差，不要尝试LoRA。

### 6.5.2 离散动作表示的VLA
离散动作这块，其实最早的工作就是openVLA，当然这个论文大致速览一下就好了，不用详细看，因为现代的VLA架构不基于离散动作了
但是离散动作是否有值得一看的部分呢，也有，你只需要看FAST（也是pi系列的，这是pi05模型的KI预训练基础），这里pi05、KI（上边提到的知识隔离knowledge insulation）会用到，我们自己做训练基本不会用到，只要懂这个就好～
pi05对于模型的预训练，用的就恰恰是FAST

### 6.5.3 pi05相关技术拆解
<img width="2542" height="1076" alt="image" src="https://github.com/user-attachments/assets/4c3a5ff1-088a-4f8e-9ce3-ffa26f9ea982" />

#### 6.5.3.1 subtask
pi05中还有subtask这么一个输入，这是直接提供给VLM部分的，这个也可以关注一下，但是实际上，不太好用只能说，而且openpi和lerobot压根就没实现这部分，即使你自己实现了，很多时候模型也会胡乱说话，除非你做密集的数据标注保证每次生成都有subtask约束，这样密集标注subtask很耗费人力。

#### 6.5.3.2 metadata
这个主要的作用是标注数据质量和其他一些元数据，数据质量方面，它可以利用模型自身vlm->action exp的注意力causal的方式在推理阶段提升模型表现。
原理上大概就是：差的数据通过metadata标注之后，VLA模型吃进去的时候知道自己吃的是不是依托答辩，万一吃进去答辩，那这个因果关联就会发挥作用，如何利用它这个因果关联呢，很简单，在推理阶段你直接用最高的metadata（可以配合CFG（无分类器的引导））输入，这样模型生成的action也就自然产生的是metadata最高的条件分布下的抽样了。

- 当然这部分我们没有做过详细实验，因为我们并没有大量的数据标注员以及大量的数据，在少量数据上显然不会起到太明显的效果。

#### 6.5.3.3state输入
（建议state和metadata得看pi05的架构图来理解，就知道该放在哪里了）
核心动机：VLM的容量很大，所以VLM也需要学动作，pi05要对VLM做预训练，所以state输入自然也得往左移。
不同点：对比pi0，pi05变成了语言state表示而非连续state表示，因为要让VLM学会动作生成所以需要放到语言里，而不是pi0那样：放到action expert前、VLM后

#### 6.5.3.4 KI: 防止训练action时候把VLM的能力搞崩
- 训练VLM的时候需要注入动作信息，所以会用到FAST技术
- 训练action的时候需要避免破坏subtask生成能力，所以需要stop-gradient
论文链接：
KI[knowledge insulation](https://arxiv.org/abs/2505.23705)


### 6.5.4 连续动作执行
#### 核心动机
- 实时
- 训推一致
熟悉大模型的话你应该对大模型的stream持续流式生成并不陌生，这是因为它是单向注意力架构，所以后续的token之需要依赖前面所有的token，这是他对语言问题的建模方式使然，其天然具备连续生成能力
但是pi0架构却不具备这样的能力，因为pi0模型（包括pi05 pi06 pi07）并不具备历史，这样连续生成+执行就变得看起来不可能了
但每个架构有每个架构的解法，pi系列模型的解法（或者说是所有diffusion policy系列的模型的解法）就是RTC
#### 6.5.4.1 train time RTC
实现起来非常简单，之需要前缀注入就好：
参考这个[训练阶段RTC](https://arxiv.org/pdf/2512.05964)
#### 6.5.4.2 inference time RTC
实现起来复杂一些，涉及到监督式的梯度引导，不是计算参数梯度而是计算输入梯度。
不建议用这个技术：虽然看起来推理时RTC听起来简单，但是时间开销会增加1倍而且实现起来复杂：
参考这个[推理阶段RTC](https://arxiv.org/pdf/2506.07339)

RTC是现代VLA的必备项，虽然很多工作省略了这个部分，但根据我们的实践经验看来，RTC至关重要，它直觉决定了某些任务、尤其是全身VLA的任务成败的关键
RTC做的不仅仅是实时执行，而是保证了训推的一致性，只要RTC做得好，你任务就能成，反之如果你直接用时间融合（temporal ensemble）方式，模型可能做出不同决策，融合到一起“打架”导致模型行动缓慢甚至卡死在原地不动。所以为了避免其原地不动的卡死问题，直接上RTC就好。

openpi内需要自己实现RTC，包括RTC训练和推理两个部分，而lerobot应该已经开发好了，你可以拿来直接用

### 6.5.5 关于历史
前面说了，现在VLA基本都没有历史，关于历史的工作也有很多，[MEM，pi官方的历史记忆实现](https://www.pi.website/download/Mem.pdf)可以参考这篇MEM论文，
核心动机：显然我们做历史肯定不能直接把所有历史信息全部发给VLM，而是尽可能先融合、再放进vlm，因为图像token位本身就是高度冗余的，比如一个224 224的图就包含了256个token，显然关键的信息并不会用到这么多。
而VLM网络恰恰有一部分叫做SigLip，如果你不知道siglip也没关系，它其实就是CLIP的升级版，干的事情就是把图像嵌入成token
所以MEM的改法很简单，不改变、新增任何可训练参数，直接改SigLIP的ViT结构，
分为，1 时间注意力、2 空间注意力
- 时间：每隔4层新增式引入一个时间注意力计算，只对历史同位置的token做注意，不跨patch位
- 空间：其余的空间注意力保持不变，完全一致，仍然是每个时间点的自注意力，不做时间上的注意力计算，和本身siglip行为一样
- 参数量最终不变，可训练参数还是那么多

实测，我们测试引入10frames的额外历史计算（11 frames in total），只会让计算时间从100ms变到300ms（3倍），而不是按照n2复杂度的约121倍，这个是在4070s上测的，在咱们HPC买的两台4090上测试效果一定只会更好

### 6.5.6 关于人在回路与强化学习
VLA的强化学习和其他不太一样，因为VLA用的是DiT策略，所以没法直接用PPO。
#### 6.5.6.1 RL（pi*06、kai0、DSRL）
目前强化学习包括pi*06和kai0等工作，如果想看的话建议就看pi06和kai0，pi*06里面的"*"的含义就是强化学习的意思，蹭大模型领域强化“*”的热点（bushi）
##### 6.5.6.1.1 pi06
绝对价值估计，直接估计剩余步数，预处理计算advantage，之后利用advantage+阈值eps做判断，生成二值化优势，因为二值化方便后面做CFG，不用担心过拟合到advantage上
##### 6.5.6.1.2 kai0
pair wise的价值估计，天然就是这个pair组成的advantage
##### 6.5.6.1.3 DSRL（这个我们没做过）
##### 6.5.6.1.4 IQL
IQL：甚至我们还做过IQL（一种以前很火的离线强化学习方法，说是离线强化领域的“PPO”），结合AWR（优势加权）训练，在push T里效果是有的，比如在不完美的400 episode rollout数据上能达到90%+而常规非AWR（不带IQL）只有80%+
但在真机上，强化学习显然也得需要大量的rollout episodes才能有明显效果
比如kai0说他们用了20小时数据
至少我们用的3小时内的数据来看，它明显能学会value（or advantage），你可以在可视化中看出明显的随任务进度进步的上升趋势📈，但是训练出来的模型至少在叠衣服数据上没那么明显的效果
##### 6.5.1.5 RL token
这是4月那会pi的工作，核心思想是既然DiT直接做不了强化那就额外加一个小的action expert

#### 6.5.6.2 高质量dagger
这个很核心！！！！dagger就是HG-dagger，就是在模型执行过程中，人工介入修正，让模型学会从错误状态中恢复，或者提前避免错误
要求我们做到三点：
- 1 知道模型在哪里容易出错，在模型执行的过程中**提前**避免错误，直接接管，做出一连串**完美**动作示教
- 2 模型已经出错，或者我们已经位于出错状态，那就让模型多学从已经错误状态中恢复正确行为的行为
- 3 完美数据：剪辑！！！把坏的动作剪掉，不要进模型学习，不要让模型学会坏的动作



# 7. 真机部署
```
"Sim2Sim2Real"
Isaac Gym\Lab -> Mujoco -> Real world
```

仿真到现实还是有一定差距的，因此可以现在[Mujoco](https://github.com/google-deepmind/mujoco)中进行预部署，这个仿真环境相较于Isaac系列会更接近真实世界（大概吧）。如果机器人用的是宇树的机器人的话（比如go2，go1或者g1），记住安全第一，记得先sim2sim到[链接-unitree mujoco](https://github.com/unitreerobotics/unitree_mujoco)，unitree mujoco中所有消息的收发方式、消息类型、关节顺序等与实机完全一致，在unitree mujoco中成功基本在实机中也能成功。

## 7.1 Q&A 现实与仿真之间的gap 
1. **现实的传感器存在误差**。    解决方法：在仿真中往观测数据里加噪声（legged gym/Isaac lab已实现）

2. **有些观测值没有传感器可以获得，比如线速度，周围地形的高度**。      解决方法：1.设计相关算法获得
这些数据，比如使用里程计估计线速度，使用SLAM建图估计周围高度    2.在仿真中使用蒸馏方法，利用现实中可以获取到的信息估计无法直接获取的信息（ legged gym/Isaac lab未实现）

3. **各种复杂地形**。    解决方法：仿真里的地形尽量全涵盖，只要让模型见过，基本就能正常通过

4. **机器人质量、关节摩擦、足部与地面的动静摩擦力不确定**。    解决方法：域随机化，也即仿真里并行跑的环境，设置不同的物理参数，让网络学会各中物理参数下应该怎么做，例如设置随机的摩擦力、kp kd的值、身体的质量等




# 8. 写在后面
希望你们来到了HPC Robotics Lab，可以真正学到东西，而不是三天打鱼两天晒网，遇到不会的欢迎和我们交流

WeChat: zsy15241308736
