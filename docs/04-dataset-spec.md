# 数据集规范 v0.1（设计稿）

## 逻辑表

| 表 | 主键及关键字段 |
| --- | --- |
| merchants | merchant_id；行业、经营年限、规模分层 |
| orders | order_id；merchant_id、buyer_id、ordered_at、amount、currency、status |
| transactions | transaction_id；payer_id、payee_id、occurred_at、available_at、amount、currency、type、order_id（可空） |
| fulfillment | event_id；order_id、event_type、occurred_at、available_at、evidence_ref |
| refunds | refund_id；order_id、transaction_id（可空）、amount、currency、occurred_at、available_at |
| assessments | assessment_id；merchant_id、cutoff_at、window_start、event_kind、coverage |
| labels | label_id；target_type、target_id、period_start、period_end、label、evidence_ref、confirmed_at、annotator、confidence |

金额使用十进制定点或最小货币单位，禁止混合币种直接求和；时间统一时区并保存来源时区。明确唯一键、关联基数和重复结算，避免订单项/支付多对多连接造成金额膨胀。

所有记录保留 source_id、source_version 与 provenance（real / semi_synthetic / synthetic）。匿名标识在授权范围内一致映射，不提交映射表。真实数据缺失字段保留缺失，不用合成值伪装成真实观测。

## 标签

- confirmed_abnormal：独立证据支持异常，限定适用对象与期间。
- verified_normal：在注明的核验范围内支持正常。
- suspected：规则或部分证据支持，不能作为金标准。
- unknown：未核验或证据不足。

两人独立试标后记录一致率和分歧；无法解决的分歧保留不确定。正式测试集尽可能独立复核，并记录标签确认延迟。交易级标签和商户级标签分开，不能无依据传播。

必须覆盖促销、季节波动、批发结算、正常退款和合法关联往来等困难正常情形。核验样本同时包含风险抽样与随机抽样，记录抽样概率；否则不能直接估计总体异常率和总体效果。

## 时间与划分

- 特征仅使用 occurred_at 与 available_at 均不晚于 cutoff_at 的信息。
- 事后调查可形成标签，事后退款和处罚等不能进入当时特征。
- 分别设置未来时段测试与未见商户/团伙测试，报告各自业务含义。
- 同一商户、团伙或同一底稿生成的变体按评测目的分组；禁止近重复跨集合。
- 归一化、缺失填补、特征选择与图构建遵循训练/时点边界；图中不能偷看未来边或标签。
- 固定测试集与划分版本，阈值仅在验证集选择。

## 数据验收

检查主键唯一、关联孤儿、金额和时间异常、缺失率、覆盖范围、重复率、标签证据可追溯率、类别与行业分布、划分交叉污染。区分数据错误和有业务解释的异常，不机械删去所有极值。

发布数据卡：来源、许可、采样方式、对象数量与交易数量、时间跨度、标签机制、观察盲区、已知偏差、适用与不适用用途。公开集、机构内真实集与模拟集分别版本化。
