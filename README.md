# Problem-First Research

### 从真实问题出发，走向可信的方法

> 研究探索不应从“我能设计什么方法”开始，而应尽快确认：**一个真实、重要、可观察的问题是否存在，我们是否有能力研究它，现有方法又留下了什么缺口。**

这是一套面向实证研究的探索流程，适用于需要通过数据、模型、仿真器或训练系统验证主张的课题。它强调先确认问题，再解释机制、设计干预，最后扩大实验规模。

## 🧭 路线图

**发现问题 → 验证现象 → 评估可行性 → 独立复现 → 梳理方法缺口 → 区分机制 → 设计并验证方法 → 扩大实验**

全程要分清四件事：

| 判断对象 | 要回答的问题 |
| --- | --- |
| **Problem validity** | 目标问题是否真实存在、值得关注？ |
| **Instrument validity** | 数据、模型、仿真器和评测工具是否测得准、用得上？ |
| **Mechanism validity** | 对问题成因的解释是否有证据？ |
| **Research tractability** | 以当前资源和时间，是否能可靠地研究它？ |

实验工具失效，不等于机制错误；机制假设被否定，不等于问题不存在；问题真实存在，也不代表它适合作为当前主线。

---

## 1. 🔎 Problem Discovery：先找真实问题

先问：

> **学界或工业界在真实训练、部署或评价中反复遇到什么问题，而它仍未得到充分解决？**

候选问题至少要通过三道初筛：

### 有真实需求

问题应影响某种实际目标，例如性能、泛化、鲁棒性、安全、数据效率、训练稳定性与成本、长尾能力、模型扩展或实际部署。**“以前没人专门研究过”本身不是研究价值的证据。**

### 有可观察证据

问题应来自可以检查的现象，例如：

- 多篇论文反复报告的异常；
- 不同模型或数据集中的共同失败模式；
- 自己的实验里稳定出现的异常；
- 工业训练流程长期存在的困难；
- benchmark 中现有解释覆盖不了的结果。

优先从“现象已经出现，只是尚未解释或解决”出发，而不是从“理论上可能存在”出发。

### 尚未被简单解决

做一次有限的文献摸底，确认：是否已有明确方案；它是否只是标准 MTL、domain adaptation 或 continual learning 问题的直接实例；近期工作是否几乎已经完整回答；剩余部分是否仍有独立研究价值。

这一步不是完整 Related Work，只需回答：**这个问题值得继续调查吗？** 暂时不要连续多轮推导机制。

---

## 2. 🧪 Problem Validation：先验证现象

候选问题通过初筛后，先别急着设计方法。先问：

> **这个现象在我们实际拥有的实验条件下真的存在吗？**

采用 **Cheap Falsification First**：优先做成本最低、最直接的小实验。第一轮只回答现象层面的问题。例如，先检查同一批数据在不同训练状态下效果是否真的不同；不要一开始就断言差异来自 representation plasticity 下降。

### 正式实验前，填写 Instrumentability Card

- **Observable：** 现象如何被直接观测？避免依赖尚未验证的复杂 latent score。
- **Intervention：** 哪个变量能被真正操纵？尽量一次只改变一个核心因素。
- **Existing instruments：** 数据、模型、checkpoint、simulator、evaluator、label 和训练流程是否已可靠可用？“理论上能开发”不等于“现在已有”。
- **Compute cost：** 若现象不存在，最多会耗费多少 GPU hours、实验轮次、数据生成和工程时间？首轮必须足够便宜。
- **Kill criterion：** 结果出来前，先写清什么结果意味着当前 hypothesis 或 operationalization 不值得继续，避免事后不断改定义直到“做出效果”。

---

## 3. 🚦 Validation Verdict：失败要分型

小实验结束后，不要只记“成功 / 失败”。至少区分以下四种结果：

### A. PHENOMENON PASS

现象稳定存在，且强于合理的实验噪声。候选问题可以升级为 **Active Research Candidate**。

### B. SUGGESTIVE

出现了信号，但稳定性不足、不同 drive 或 seed 差异较大、效应量偏弱，或仍有 control 未排除。只做有限的补充确认，不无限追加实验。

### C. PHENOMENON NULL

当前 instrument 的灵敏度足够，但没有观测到现象。这表示**当前问题定义在当前设置下缺乏支持**，通常应冻结该方向；它不证明整个 E2E driving 中都不存在这个问题。

### D. OPERATIONAL FAIL

实验工具或干预本身未成立，例如 simulator 数值错误、probe 读不到目标、数据缺失、checkpoint 不可比、evaluator 不可信，或 intervention 实际没有发生。此时只能说**当前 operationalization 失败**，不能推出机制或问题失败。

---

## 4. 🧰 Research Tractability：问题重要还不够

一个问题可以很重要、新颖，现象也真实，但若研究它必须先修大型 simulator、重新采集数据、训练几十个 foundation models、建设复杂标注体系，或消耗无法承受的 GPU 预算，它仍可能不适合作为当前方向。

逐项评估：

- 数据和模型是否已经具备？
- evaluation 是否可信？
- intervention 是否可以控制？
- 是否能构造可信 baseline？
- 能否在数周内看到首个科学结果，而不是几个月后才开始验证？
- 总算力是否与预期研究收益相称？

真实但当前不可操作的问题，可以标记为 **FROZEN — revisit when instrumentation improves**，等工具条件改善后重访，而不是硬做。

---

## 5. 🔁 Confirmation：重要现象先独立复现

第一轮 cheap test 通过后，先检查现象是否依赖某一批数据、一个 seed 或一个特殊 split。选择信息价值高、成本低的复制实验，例如：

- 第二个独立数据子集；
- 第二组 domain pair；
- 第二条 training trajectory；
- 局部 seed replicate。

目标不是立刻完成大规模统计研究，而是确认现象具有基本可重复性。只有通过这一关，才值得投入更多时间解释机制。

---

## 6. 📚 Existing Methods & Gap：此时再做深度文献研究

现象确认后，再系统回答：现有方法具体如何处理这个问题？文献调研至少覆盖四点：

1. **Existing explanation：** 现有工作如何解释现象？
2. **Existing solution：** 已有方法具体干预了什么？
3. **Remaining gap：** 还有哪些问题没有回答？
4. **Our opportunity：** 我们能提出什么有边界、可验证的贡献？

这时再深入 Related Work、竞争性机制、跨领域类比、理论解释和 solution landscape。这样可以避免推导许多轮后才发现现象根本测不了。

---

## 7. 🩺 Mechanism Triage：先定位异常发生在哪

提出新方法前，先用简单诊断把现象拆开。例如依次检查：

**training fit → held-out generalization → old capability retention**

判断异常究竟出现在训练拟合、留出泛化，还是旧能力保持。现象位置明确后，再提出机制假设。

机制候选原则上保留 **2–3 个最有区分度的解释**，然后设计：

> **一个机制假设 + 一个能够区分它与其他解释的实验**

若无法设计区分机制的实验，该机制暂时还没有足够的研究价值。避免 H1 到 H8 不断扩张，却始终没有判别实验。

---

## 8. 🛠️ Method Design：方法要对应已确认的缺口

每个方法设计都应回答：

> **它针对前面已经观察到、并有证据支持的哪个 failure mechanism？**

推理链应当清楚：

**Observed Problem → Validated Mechanism → Method Intervention**

如果证据指向 optimization-state mismatch，才考虑 optimizer 或 update dynamics；如果真正问题是 generalization，就不应继续堆叠 optimizer 方法。方法设计应从确认过的机制缺口生长，而不是因为某个 module“看起来可能涨点”。

---

## 9. 📏 Method Validation：验证为何有效

正式方法实验至少回答三个问题：

### Effectiveness

方法有没有改善最终目标性能？

### Mechanism consistency

方法是否真的缓解了前面确认的问题？例如，若声称改善 stage-dependent learnability，就要检查 stage sensitivity 是否下降，不能只报告最终 ADE 增加了 0.2。

### Generalization

至少在以下一项上复核：第二个模型、第二个数据集或 domain、第二种训练条件、第二种数据规模。否则很难判断方法解决的是普遍问题，还是只适用于一个特定实验配置。

---

## 10. 📈 Scale Up Last：最后再扩大规模

只有前面各关都通过，才扩展到更多 seed、模型和数据，完整 training schedule、大规模 ablation、sensitivity study 和 benchmark comparison。

> **先证明值得算，再投入算力。**

不要一开始就铺开几十组实验。

---

## 🧩 一条完整的决策链

1. 真实 observation
2. Problem validity
3. Instrumentability audit
4. Cheap phenomenon test
5. Research tractability
6. Independent confirmation
7. Detailed literature & gap
8. Mechanism discrimination
9. Method design
10. Method validation
11. Scale-up & generalization

每一关都要留下证据、判断和下一步；不要把后续方法实验的结果倒过来当成前期问题存在的证明。

## 🧱 三条硬规则

### Rule 1 — 不允许 Method First

现象尚未验证时，不讨论新 loss、新 scheduler、新 module、新 score、curriculum 或 optimizer trick。

### Rule 2 — 小实验可以否定操作化，不能轻易否定整个问题

晋级需要较强证据；判定问题失败也同样需要较强证据。不要因为一个 probe 失败就宣布问题不存在。

### Rule 3 — “重要但做不了”不等于当前的好方向

当前主线需要同时具备：

**Importance × Evidence × Novelty × Tractability**

任何一项接近零，都不适合作为当前主线。

## ✅ 新方向的 8 个检查问题

1. 真实 observation 是什么？
2. 为什么它重要？
3. 现有证据能否说明它不是偶发现象？
4. 最便宜的 phenomenon test 是什么？
5. 现有数据、模型和工具能否完成？
6. 什么结果意味着停止？
7. 如果 phenomenon 成立，现有方法还缺什么？
8. 我们的 intervention 为什么正好针对这个缺口？

如果第 4–6 题还答不清楚，先不要继续做机制和方法设计。

## 🌱 最后的研究观

研究探索不该追求尽快找到一个看起来新颖的方法，而应追求尽快确认一个**真实、重要、可研究的问题**。

前期最重要的成果，不是“方法已经想出来了”，而是能够有证据地说明：

> **这个问题确实存在，我们能够研究它，而且现有方法还没有解决它。**

到这一步，方法创新才真正有根。
