# SABRE 论文汇报讲稿

## Slide 1 封面

本次汇报介绍论文 *Tackling the Qubit Mapping Problem for NISQ-Era Quantum Devices*。论文由 Gushu Li、Yufei Ding 和 Yuan Xie 提出，核心内容是 SABRE 算法，即一种面向 NISQ 设备的量子比特映射方法。汇报将围绕真实硬件约束、qubit mapping 问题定义、SABRE 的算法机制以及实验结果展开。

## Slide 2 目录

汇报分为五个部分。首先说明 NISQ 硬件连接受限为何会带来 mapping 问题；其次给出 mapping 的形式化含义；然后分析该问题的主要困难；第四部分介绍 SABRE 的关键方法，包括 front layer、启发式代价函数、reverse traversal 和 decay effect；最后讨论论文实验结果、局限性与总结。

## Slide 3 问题背景

理论量子线路通常以 logical qubit 为对象描述，线路图中可以出现任意两个 qubit 之间的双比特门。但真实 NISQ 芯片不是全连接结构。以 IBM Q20 Tokyo 为例，physical qubit 之间只存在有限耦合边，只有相邻的物理比特才能直接执行 CNOT 等双比特门。

这带来三个直接影响。第一，连接有限，原始线路中的双比特门可能无法直接落到芯片上。第二，双比特门错误率通常高于单比特门，额外操作会累积误差。第三，线路深度越大，退相干风险越高。因此，mapping 并不是简单的格式转换，而是会直接影响最终线路保真度的编译优化问题。

## Slide 4 Mapping 定义

Qubit mapping 可以理解为“初始放置加中途换位”。输入包括两个部分：一是逻辑量子线路，二是目标芯片的耦合图。输出则是满足硬件连接约束的可执行线路。

具体而言，编译器首先需要把 logical qubit 映射到 physical qubit 上。如果某个双比特门的两个 logical qubit 当前被映射到不相邻的 physical qubit 上，就需要插入 SWAP 改变映射关系。SWAP 之后，原本无法执行的双比特门可能变成可执行。因此，mapping 的输出不仅包含初始映射，也包含执行过程中插入的 SWAP 序列。

## Slide 5 问题难点

这个问题的难点不在于是否可以插入 SWAP，而在于插在哪里、插哪个 SWAP。首先，搜索空间非常大。若有 n 个 logical qubit，其到 physical qubit 的排列组合会快速增长，论文也指出该问题是 NP-Complete。

其次，初始映射非常敏感。一开始放置不合适，后续可能需要连续插入 SWAP 来补救。再次，优化目标存在冲突。减少门数通常有利于降低错误累积，但降低线路深度也很重要；为了提高并行性，有时需要接受略多的操作。

因此，论文的策略不是穷举所有 mapping，而是在每一步只评估与当前执行相关的候选 SWAP，并利用启发式函数做近似选择。

## Slide 6 SABRE 总体思路

SABRE 的全称是 SWAP-based BidiREctional heuristic search。它的核心转向是：不直接在完整 mapping 状态空间中搜索，而是逐步搜索和选择 SWAP。

算法整体包括三次遍历。第一次是临时正向遍历，从随机或简单初始映射出发，生成一条可行路径并得到最终映射。第二次是反向遍历，将线路倒序处理，用第一次得到的最终映射作为起点，反推出更适合原线路开头的映射。第三次是最终正向遍历，以更新后的初始映射生成最终硬件可执行线路。

贯穿这三步的是同一套局部决策机制：用 front layer 找当前受阻门，用启发式代价函数评价候选 SWAP，并通过 decay 控制线路深度。

## Slide 7 复杂度分析

传统 mapping 搜索需要在完整 logical-to-physical mapping 状态空间中搜索，因此状态数量随 qubit 数呈指数级增长。N 稍大时，运行时间和内存都会迅速变得不可接受。

SABRE 的不同之处在于，每一步只评估与 front layer 相关的局部候选 SWAP。论文中给出的复杂度口径是：每个 two-qubit gate 最多为 \(O(N^{2.5})\)，若原线路中有 \(g\) 个双比特门，则整条线路 worst-case 为 \(O(N^{2.5}g)\)。这种做法牺牲了全局最优保证，但换来了可扩展性，使较大规模 NISQ 线路能够在真实拓扑上完成编译。

## Slide 8 算法工作流

这一页将 SABRE 的完整流程展开。三次遍历分别对应临时正向、反向和最终正向。每次遍历内部，都使用 SWAP-based heuristic search。

在局部搜索中，算法先查看 front layer，即当前依赖已经满足、理论上可以执行的双比特门集合。如果门的两个 physical qubit 相邻，就直接执行；如果不相邻，就生成候选 SWAP。随后用启发式代价函数评分，并选择代价最低的 SWAP 插入线路。插入后，当前 mapping 和 DAG 状态都会更新，再继续检查新的 front layer。

## Slide 9 Front layer

Front layer 是 SABRE 限制搜索范围的关键。量子线路中的双比特门存在依赖关系，可以表示成 DAG。Front layer 指当前没有未执行前驱的双比特门集合，也就是从逻辑依赖角度看已经可以执行的门。

如果 front layer 中某个门对应的 physical qubit 已经相邻，算法直接执行该门；如果不相邻，就只考虑相关 qubit 附近的 SWAP，而不是枚举全芯片所有交换。这样可以显著减少候选空间。

图中的小例子说明了这一点。若当前 front layer 是 `CNOT(q0,q2)`，并且当前映射为 `q0@P0, q2@P3`，则候选 SWAP 只需要从 P0 和 P3 的邻边中产生。后续再用启发式函数判断哪个 SWAP 更合适。

## Slide 10 启发式函数

SABRE 用启发式函数为候选 SWAP 打分。其核心思想是：当前门的物理距离要尽量降低，同时不能让后续门显著变差，并且要避免近期重复移动同一批 qubit。

公式中，\(F\) 表示 front layer，\(E\) 表示 extended set，也就是 front layer 后面的一小批后继门。\(D\) 是物理耦合图上的最短路距离，\(\pi\) 是当前 mapping。第一项计算当前门集合的平均距离，第二项计算未来门集合的平均距离，并由权重 \(W\) 控制其影响。论文实验中 \(W=0.5\)。

最外层的 decay 项用于惩罚近期参与过 SWAP 的 qubit。如果某个候选 SWAP 涉及刚刚移动过的 qubit，它的代价会升高。最终，算法选择 \(H\) 最小的候选 SWAP。

## Slide 11 Reverse traversal

Reverse traversal 用于改进初始映射。需要强调的是，这不是让真实量子机器反向运行程序，而是编译阶段的倒序分析。

算法先从临时初始映射 \(\pi_0\) 正向遍历原线路，得到最终映射 \(\pi_f\)。然后将线路反向，用 \(\pi_f\) 作为反向遍历的初始映射，得到新的映射 \(\pi_r\)。最后再以 \(\pi_r\) 作为原线路的初始映射进行最终正向遍历。

这一方法有效的原因是：反向线路中最后处理的门，对应原线路开头的门。因此，反向遍历得到的 \(\pi_r\) 能更好地服务于原线路开头，同时也吸收了后续门的信息。

## Slide 12 Decay effect

Decay effect 解决的是门数与线路深度之间的权衡。这里的 decay 不是物理意义上的衰减，也不是距离随时间衰减，而是对近期参与过 SWAP 的 qubit 临时增加代价。

如果没有 decay，算法可能连续移动同一批 qubit。这样虽然可能减少某些局部门数，但 SWAP 之间会形成依赖，难以并行执行。加入 decay 后，近期参与过 SWAP 的 qubit 代价升高，算法更倾向选择不重叠的交换，从而提高并行性、降低线路深度。

参数 \(\delta\) 控制这种倾向。较大的 \(\delta\) 更偏向降低深度，较小的 \(\delta\) 更偏向减少额外门数。

## Slide 13 实验设置

论文在 IBM Q20 Tokyo 的 20-qubit 拓扑上进行评估，并使用多类通用量子线路 benchmark。对比方法是 BKA，也就是当时的 Best Known Algorithm。

评价指标包括四类。第一是额外门数，因为 SWAP 会分解为多个 CNOT，额外门越少通常越好。第二是线路深度，它影响执行时间和退相干风险。第三是运行时间，反映编译器能否处理大规模线路。第四是内存占用，用来衡量算法在大 benchmark 上是否可扩展。

## Slide 14 实验结果

实验结果显示，SABRE 的优势不是单一指标，而是在线路质量和编译资源之间取得了较好平衡。

在门数方面，对于 large 和 qft 类 benchmark，SABRE 平均减少约 10% 的额外门；在小线路上，额外门数可减少 91% 甚至完全消除。在运行时间方面，部分 benchmark 有数千倍量级加速，例如 qft16 约 5652 倍，qft13 约 7396 倍。在内存方面，BKA 在 ising16 和 qft20 上超过 378GB 内存而无法完成，SABRE 仍能以约 300MB 内存和约 0.1 秒完成。

需要注意，SABRE 不保证全局最优。它的意义在于用 reverse traversal 改善初始映射，并用局部 SWAP 搜索换取可扩展性。此外，少 SWAP 不必然等于最高保真度，真实设备还需要考虑不同 qubit 和 edge 的噪声差异、动态校准和串扰。

## Slide 15 总结与展望

总体来看，SABRE 的贡献在于将 qubit mapping 从全局穷举式搜索推进到可扩展的硬件感知启发式编译。

从效率上看，它用启发式搜索快速找到近优解，避免枚举所有 mapping。从可扩展性上看，它将搜索限制在局部候选 SWAP，使方法适配更大规模量子硬件。从方法论上看，reverse traversal 利用可逆性改善初始映射；decay effect 则把深度控制纳入代价函数。从工程角度看，SABRE 已成为 NISQ 编译中的重要基线。

后续方向包括 noise-aware mapping、hardware-aware compilation 和 learning-based mapping。也就是说，未来可以进一步将噪声模型、门保真度和动态校准信息纳入 cost function，使 mapping 更接近真实硬件运行目标。

## Slide 16 结束页

以上是本次关于 SABRE 量子比特映射算法的汇报。谢谢各位老师和同学。
