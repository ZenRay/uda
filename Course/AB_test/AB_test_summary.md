总结这篇文章，主要在学习 AB 测试的过程中遇到了两个方面的问题。其中之一是，课程讲解是将 AB 测试的流程交叉到分析过程、概念讲解等过程中，以致于对 AB 测试的流程没有形成一个完整的体系；另一个问题是，AB 测试中用到的统计学相关知识。在课程中统计学的讲解和公式，表面上是很违背“直觉的”，所以需要一个合适的切入角度去理解 AB 测试中用到的统计学知识。

因此本次根据课程内容，网上查阅的相关资料对 AB 测试的流程进行一个梳理。同时在最后将统计学方面中较难理解的部分，进行一个梳理，提供一个可行的切入角度。

[toc]

## AB 测试的流程
在明白 AB 测试的流程之前，需要先从 AB 测试是什么和为什么使用的角度进行说明。简单来说 **AB 测试**就是为了验证在先验条件的存在的情况下，进行新的变更是否合理和可行以达到优化的目的。使用 AB 测试的方式能能够度量变更对某些指标的变化，是变更更具有合理性依据更充分。

AB 测试也存在 **不适用的场景**：1）对没有明确参照的试验，AB 测试是基于先验条件的优化，如果没有一个参照对比是无法进行测试。2）数据获取时间长，AB 测试一般都是进行小规模快速的试验，所以对于数据获取的单周期较长的试验不太适用。

对于这类不适用场景情况下，可以采取的**解决方案**：1）通过查询和观察分析日志，进行假设检验，了解用户行为改变原因，使用其他试验方法来完成随机化和试验，进行前瞻性分析。2）其他方法，例如用户体验研究、焦点小组、调查以及人为评价——可以完成深入的定性数据。可以参考[ Additional Techniques for Brainstorming and Validating Metrics ](https://www.udacity.com/api/nodes/3954679115/supplemental_media/additional-techniquespdf/download)

对于可以使用 AB 测试的试验，一般流程可以归结为一下几个步骤${^{[1]}}$：
1. 选择和描述指标
2. 确认显著性水平（${\alpha\ level}$ ），统计功效（${statistical\ power=1-{\beta}}$），实际显著性水平（${practical\ significance\ level}$）
3. 计算需要的样本容量
4. 对试验组和控制组进行分流，运行测试
5. 分析结果以及得出有效的结论

下面将从以上五个流程步骤，进行分别梳理。

### 1. 选择和描述指标
选择合适的指标，需要理解指标 **敏感性** 和 **稳健性** ${^{[2]}}$ 的良好直觉的基础上，通过相应的方式来衡量指标是否可用。在 AB 测试过程中，主要有两类指标：

* 不变指标（invariant metrics）：在试验中选取的这样的特征，它能够确认试验组和对照组的容量是否满足相等，而且它不能受到变更的直接明显的影响。它的 **评估方式** 是通过 **完整性检查**（sanity check）来确认是否符合不变指标的要求。一般包括 **总体规模指标** 和 **不会变化的指标**。
* 评估指标（evaluation metrics）：在试验中选择的特征，它能够反应出变更效果。总体上来说，它指的是分为 4 个类别的指标：1）计数和合计类指标；2）分布类型指标（例如平均值、中位数以及百分位数等）；3）概率性和比率型指标（probability & rates，例如 CTP 和 CTR）；4）比例型指标，它是指利用两个指标计算出的百分数，合并的一个新指标。

在建立合适的指标的过程中，需要确认高级业务指标——在针对多团队工作的时候，这样可以统一目标。通常首先应该是简单明确的阐释指标以使指标能够被容易理解；之后对指标细节进行细化，确定如何明确测量指标；再进一步进行数据测量，确认该指标使用方法。对于指标确认还需要注意，对众多指标需要进行相应的评估——因为针对使用目标不同，可能会需要对指标进行调整。当然在团队中的指标使用时，需要注意使用复合指标以避免同一个团队使用不同指标造成的目的分散。复合指标一般被称为目标函数或者 总体评估标准（ OEC 详情见[该文章](http://www.exp-platform.com/Pages/hippo_long.aspx)）。

### 2. 选择显著性水平、统计功效、实际显著性水平${^{[3]}}$
在 AB 测试中，通常使用的统计显著性水平为 ${0.05}$，统计功效设置为 ${0.80}$，而实际显著性水平需要根据不同的试验进行设定。这三者一方面在后续中确认试验规模中需要使用；另一方面，需要使用三者进行统计学分析，得出合适的结论。其中实际显著性水平，主要是说明了当检测到变更时，期望多大的变化量才会实施变更——对于这点是基于战略层面以及实际的收益比进行考量。

### 3. 计算试验样本容量
在分析指标的变异性的时候，可以发现指标选择、分流单元（unit of diversion）的选择以及总体的选择都会影响结果。确认样本容量${^{[4]}}$的时候需要从三者进行考虑：

* 主体（`Subject`）：试验中的分流单元说明了试验的主体，即试验中需要用到的测试和比较对象。分流单元主要用的对象${^{[5]}}$是：1）用户 ID；2）匿名 ID，例如 `Cookie`；3）`Event`。其他不常用的有设备 ID，IP 等。

* 总体（`Population`）：这个是为了理解试验中使用的主体来自于合适的总体，保证分流的准确性，也是为了确保试验的有效性。例如，在实际情况下，变更时针对某个国家、某个语言下的变更——具体可以参考 **队列**（`Cohort`）。

* 保证试验得到快速反馈 因为 AB 测试的特点是快速得到有效的试验反馈，然后要保证试验分析的有效性同时获得良好的显著性分析。因此需要考虑怎么确保使用合适的 ${\alpha}$、${\beta}$ 以及实际显著性的情况下，使用合适样本容量。

在课程中使用了[在线计算器](http://www.evanmiller.org/ab-testing/sample-size.html)，利用确定的参数来确认样本容量。使用工具时请注意以下概念：**基准转换率**（ `baseline conversion rate` ）—— 指变更之后的预计点击概率；**最低可探测效果**（ `minimum detectable effect` ）—— 指实际显著性，相当于 ${d_{min}}$ 。最低可探测效果可以选择绝对数值变更和相对数值变更，但是一般选择绝对数值变更；其他参数 ${\alpha}$ 和 ${\beta}$ 根据试验初期确定的值——一般情况下 ${\alpha}$ 一般为 `5%`和 ${\beta}$ 一般设置为 `80%`。


### 4. 试验组和对照组分配及运行试验
在进行了以上流程之后，需要考虑怎么样运行试验——主要指需要试验持续时间以及试验实施时间两个方面；同时在已经明确了样本容量之后，考虑到需要“抽样”的方式进行多次试验，所以通常把试验样本的容量进行拆分成多次进行试验${^{[6]}}$。这样导致需要结合分配给试验的流量比例和试验样本规模，综合考虑试验 **持续时间**（Duration），**曝光时机**（Exposure）以及 **学习效应**（Learning Effect）${^{[7]}}$。

### 5. 分析试验结果得出有效结论
AB 测试的目的就是为了验证变更是否可行，但是在试验过程中可能收到多种因素的影响都可能导致结果不可信，因此需要去验证试验的数据是否可信。在分析结果的过程，包括以下内容：

* 完整性检查（`Sanity check`）

	在完整性检查是通过验证 **不变指标** 在试验中是否发生了变化。完整性检查是第一步，如果无法通过完整性检查，那么就不能继续进行统计分析。需要检查失败原因，以便后续进行试验以及提供经验教训。一般分析原因，可能用到回顾性分析和分析学习效应影响
* 分析数据

	进行参数型统计分析，就是检验是否有否具有显著性差异。对单一评估指标进行分析，有助于了解试验中存在的问题（例如设计是否合理）以及了解用户对变更的反应。完成参数性统计分析之后，需要从非参数型分析的方法进行总体分析。需要 **注意**：当面对参数型假设检验和非参数型符号检验结论冲突时，可以检查数据是否存在辛普森悖论（Simpson's Paradox）——一般发生这种情况可能的原因：1）试验设置不正确；2）变更在新用户和老用户之间存在差异。

	对多评估指标分析，需要注意实际非显著性的指标被当作显著性——导致存在这个问题的原因是非相关的多个指标增加了最后结论的显著性${^{[8]}}$。针对这类显著性被放大的解决方法：1）`Bootstrap`；2）`Bonferroni Correction`，它是将显著性水平除以指标个数，以降低单个指标的显著性水平。但是该方法可能导致分析结果过于保守——对相关性指标不建议使用；3）控制 `Familysise Error Rate` (FWER)；4）控制 `False Discovery Rate`(FDR)。注意：在实际分析中可能指标之间的显著性方向不一样——例如，在试验假设中期望是 DAU 和 用户在线时间都增加，但是实际分析可能是 DAU 减少了而用户在线时间增加。对于这类问题，一般是通过设置总体评估标准（即 OEC，Overall Evaluation Criterion），但是 OEC 是帮助理解商业目标以及平衡指标的时间和点击问题，但是它并不能帮助做出变更决定。
	
	在完成以上的统计显著性分析之后，需要通过实际显著性分析来确认变更的实际影响以及实际可实施的可能性。最后得出有效结论需要从理解变更，变更的影响以及产生结果的原因等方面着手阐释，并最后得出是否可以执行变更的结论。
	
### 6. 其他需要思考的角度
在本次总结中，没有对风险以及试验中涉及的伦理问题进行总结。这些都是影响试验的重要因素，在试验设计初期需要考虑。详细内容可以参考课程中的内容。


## AB 测试中统计学内容
首先需要明确一点，在课程中 AB 测试使用的统计学显著性检验方法为 **${z}$ 检验** ${^{[9]}}$。课程中用到的统计学内容的步骤：1）试验过程中显著性分析；2）试验结论显著性分析过程；3）非参数型的符号检验，进行展开解释。

### 1. 试验过程中显著性分析——完整性检查
完整性检查是利用不变指标来检查试验是否有效的过程。首先在进行流量分流到试验组和对照组对象的过程中，是随机分配到两者中（即 ${0.5}$ 的概率）——这里符合一个 **二项分布** 的特征。因此只需要分析对照组或试验组是否满足这个二项分布的置信区间，相应的计算公式如下：

* **期望**：${E=n*p}$
* **标准偏差**：`Standard Deviation`，${SD=\sqrt{n*p*(1-p)}}$
* **标准差**：`Standart Error`, ${SE=\frac{SD}{n}=\sqrt{\frac{p*(1-p)}{n}}}$
* **边际值**：`Margin`，${margin=SE*z^*}$
* **置信区间**：`Confidence Interval`，${CI\in[p-margin,\ p+margin]}$
* 实际概率：指对照组或控制组占总体的比例（这里仅对控制组作计算），${p^*=\frac{n_{contr}}{n}}$

以上公式中，${n}$ 指 ${n_{total}}$，为试验组和对照组的样本容量求和 ${n_{total}=n_{contr}+n_{exp}}$；${z^*}$ 指的是在置信水平 ${\alpha}$ 的条件下得到的 ${z_{score}}$。

检查过程中需要分别计算每个不变指标的值，比较某个指标 ${p^*}$ 是否在该指标的置信区间 ${CI}$ 中，如果满足要求那么说明该指标通过了完整性检查。在实际中，可以通过另一种方式来计算——${p_{exp}}$ 与 ${CI_{contr}\in[p_{contr}-margin_{contr}, \ p_{contr}+margin_{contr}]}$ 比较的方式（实际中可以利用 ${p_{contr}}$ 和 与试验组进行比较）。以对照组的计算方式如下：

* **概率**：${p_{exp}=\frac{x_{exp}}{n_{exp}}}$，${p_{contr}=\frac{x_{contr}}{n_{contr}}}$
* **标准差**：`Standart Error`, ${SE_{contr}=\sqrt{\frac{p_{contr}*(1-p_{contr})}{n_{contr}}}}$
* **边际值**：`Margin`，${margin_{contr}=SE_{contr}*z^*}$
* **置信区间**：`Confidence Interval`，${CI\in[p_{contr}-margin_{contr},\ p_{contr}+margin_{contr}]}$

通过这种方式进行分析完整性检验，如果 ${p_{exp}}$ 在 ${CI}$ 中那么就通过了完整性检验，否者即不能通过完整性检验。

### 2. 试验显著性分析过程——显著性分析（效应分析）
在进行显著分析之前，需要分析指标的分组单元和分析单元是否一致${^{[10]}}$——当两者一致时说明分析方差和实证方差匹配。显著性分析主要包括统计学显著性分析和实际显著性分析，下面对两者分别说明：

* 统计显著性 `Statistics Significance`
	
	统计显著性分析是针对评估指标的分析，为了检验试验组和对照组中产生了差异性。在试验中，零和假设为试验组和对照组之间概率没有差异性即 ${\hat{d}=\hat{p_{exp}}-\hat{p_{contr}}=0}$；备选假设两者概率存在差异。如果利用差异值 ${\hat{d}}$ 计算相应的置信区间，假设区间内不包括 ${0}$，说明两者具有统计显著性，那么就可以拒绝接受零和假设；反之，则不可以拒绝零和假设。分析过程是基于双样本差异分析，具体过程如下：
	
	* **差异值**：`Difference`, ${\hat{d}=\hat{p_{exp}}-\hat{p_{contr}}}$
	* **合并概率**：`Pool Probability` 指试验组和对照组中发生发生占总体的概率 ${p_{pool}=\frac{x_{cont}+x_{exp}}{n_{contr}+n_{exp}}}$
	*  **合并标准差**：`Pool Standard Error`，一般合并方差的计算使用合并标准偏差来计算，公式是 ${SE_{pool}=\sqrt{SD_{pool}^2/n_{contr}+SD_{pool}^2/n_{exp}}}$，该公式中 ${SD_{pool}=\sqrt{p_{pool}*(1-p_{pool})}}$
	*  **边际值**: `Margin`，${margin=SE_{pool}*z^*}$
	*  **置信区间**：`Confidence Interval`，${CI\in[\hat{d}-margin,\ \hat{d}+margin]}$
	

* 实际显著性 `Practical Significance`

	在完成统计显著性分析之后，才能进行实际显著性分析——也就是说一般分析的时候，统计显著性是实际显著性的基础条件。而实际显著性分析比较简单，一般指需要通过比较设定的 ${d_{min}}$ 值与统计显著性的置信区间——如果在在置信区间内，那么说明拒绝实际显著性假设；反之，则说明具有实际显著性。

### 3. 符号检验——非参数型检验方法
符号检验（即 `Sign Test`）,主要依据的是假设了试验成功的概率是 50% ，同时根据试验是一个二项式分布的试验。以此条件计算出概率，如果计算出的概率（ ${p_{value}}$ ）小于假设的 ${p_{critical}}$ 值，那么就是统计学显著。计算概率的方式可以使用[在线计算工具](http://graphpad.com/quickcalcs/binomial1.cfm)。

## 参考
1. [A Summary of Udacity A/B Testing Course ](https://towardsdatascience.com/a-summary-of-udacity-a-b-testing-course-9ecc32dedbb1)

2. **敏感性** 和 **稳健性**：即 sensitivity 以及 robustness，他们用于评价指标是否可用的一个参考。一般可以通过以下方式进行测量：i)使用 AA 测试；ii）回顾性分析（retrospective analysis）。一般来说这两者是逆相关，所以对于两者需要一个合适的平衡

3. 三者统计学解释，可以参考 [${\alpha}$ 和 ${\beta}$](http://www.appadhoc.com/blog/alpha-beta-statics-ab-test/)，以及[如何理解统计学中的 Power ](http://songchunlin.net/cn/2014/04/statistical-power/)：
	* **显著性水平**：在统计学假设试验中，${\alpha}$ 表示了在零和假设 ${H_0}$ 是真的情况下，发生拒绝 ${H_0}$ 的概率；是判断发生 ${Type\ Error\ I}$ (即 ${P_{reject\ null\ hypothesis|true\ null\ hypothesis}}$) 的概率。通过 ${\alpha}$ 可以计算出置信水平（ ${Confidence\ Level=1-\alpha}$ ）
	
	* **统计功效**：其统计学定义是零和假设 ${H_0}$ 错误的情况下，成功拒绝 ${H_0}$ 的概率（即 ${P_{reject\ null\ hypothesis|false\ null\ hypothesis}}$）。这里涉及到另一个术语 ${\beta}$，它和统计功效的关系是 ${\beta=1-statistics\ power}$，是判断发生 ${Type\ Error\ II}$ (即 ${P_{not\ reject\ null\ hypothesis|false\ null\ hypothesis}}$) 的概率
	* **实际显著性**：在 AB 测试中，该显著性主要是基于商业角度等方面考量，用于判断在变更带来多大的变化量的时候可以实施变更。一般是试验结合实际，进行设定的 ${d_{min}}$
	
4. 样本容量对统计学分析的时候，对显著性分析，统计功效的影响可以参见可视化模型[Understanding Statistical Power and Significance Testing ](http://rpsychologist.com/d3/NHST/)

5. 对三种常用的分流对象来说，需要从三个角度去考量：1）作引流单元是否影响用户体验一致性；2）变更是否明显直接呈现在用户面前，例如后端服务器变化；3）分析评估中是否需要考虑用户行为作为分析角度。对于三个角度都是否定的角度，那么可以参考使用 `event` 来分流——例如服务器变化测量视频加载时间变更

6. 一般情况下，很少使用单次周期的试验，通常都是使用多周期的试验。这样的好处是当面对 `学习效应（learning effect）` 的时候，试验能够安全得到有效的数据；同时从商业角度考量，对于新功能测试一般都不太希望得到竞争对手的干扰以及市场关注；同时也是为了避免测试样本容量全部不暴露在某个集中的时间短，造成数据不能得到有效数据，例如：节假日期间流量；另一方面，是体现该方法的优势——当进行多个试验任务时或针对相同功能展开的多个试验任务，如针对不同任务设置不同水平参数、相同功能的不同类型的试验，针对这类试验能够方便地进行纵向对比增加试验结论可信性

7. **学习效应**：即 `Learning Effect`，指的是当对新的变更时，因为用户差异以及用户需要花费成本去学习新的变化而导致初期表现出结果波动较大；随着时间的变化，用户行为才可能稳定

8. 该原因的统计学解释，如下：

	${P_{one\ significant\ result}=1-P_{non\ significant\ results}}$，其中 ${P_{non\ significant\ results}}$ 的计算需要结合指标个数： ${P_{non\ significant\ results}=(1-\alpha)^n}$ ，其中 ${n}$ 为指标个数。
	
9. 这里的基础条件：首先在试验中使用到的总体（Population）是足够大，同时进行分析的样本容量（Sample Size）都是大于 30（因为一般的 ${t}$ 检验的是适用于样本容量较小的试验）。因为样本容量足够大，这里可以假设通过样本几乎可以估计出总体的参数。

10. **分析单元**：它只是在分析某些指标时用到的 **分母**，是分析过程中需要确认每次的事件是否独立。例如：计算点击率指标时，用点击量除以页面查看量，其中分母页面查看量，即是分析单元。