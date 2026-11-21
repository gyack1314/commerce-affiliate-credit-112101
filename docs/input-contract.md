# 资料约定

touch_id为触点身份，known_at是平台可获知时间，发生时间用occurred_at；identity_links只在valid_from到valid_to且known_at已到的范围提供账户关联。contracts按partner、brand及生效期限定归属；eligible_kinds为click、view或code，归属窗口以支付时间向前计算。exclusive合同取有效范围内最后一个合格触点，共享合同在其允许池内按合同权重分配，exclusive优先，合同冲突须保留待裁决，不自行猜优先级。commission_rate以有效商品净支付额计算，返分余数按partner_id稳定分配。appeals只有accepted证据进入当前认定；signed_commissions保存已签认事实。recoveries只有received才能增加cash_available。对照cohort是同期所有可触达匿名账户，含从未点击和零购买者，增量推断不得将触点本身当随机化。plans限定可比组合、品牌排他期及支出，不联网投放。

## 文件与时间

所有数据都是本地合成记录，不代表新闻中的企业或交易。history.json为首批事实，updates.json为后续补充资料；后者可引用前者的对象。id标识一条事实；revises引用被更正的事实身份，不表示删除历史。known_at、recorded_at、published_at等字段表示资料可获知时间，occurred_at或effective_at表示实际发生或适用时间，二者不可互换。时间使用带时区的ISO 8601字符串，区间无特别说明均含起点、不含终点。历史分析只能采用指定截点已可得资料；空列表、null、数值零分别保留其含义。合成金额不触及真实账户。

## 数据表

### cohort

字段：id, eligible_at, country, pre_spend, returning。

### touches

字段：id, partner_id, device_id, kind, occurred_at, known_at, brand。

### identity_links

字段：id, device_id, account_id, valid_from, valid_to, known_at。

### orders

字段：id, account_id, paid_at, known_at, mature_at。

### lines

字段：id, order_id, brand, quantity, paid_minor。

### returns

字段：id, line_id, quantity, amount_minor, occurred_at, known_at。

### contracts

字段：id, partner_id, brand, valid_from, valid_to, eligible_kinds, window_days, mode, pool, weight, commission_rate。

### appeals

字段：id, order_id, partner_id, kind, touch_id, status, occurred_at, known_at。

### signed_commissions

字段：id, order_id, partner_id, amount_minor, signed_at。

### recoveries

字段：id, signed_id, amount_minor, state, known_at。

### budgets

字段：id, cash_available。

### plans

字段：id, partner_id, brand, exclusive, start, end, spend_minor。

### accepted_plans

字段：id, partner_id, brand, start, end, spend_minor。

input-schema.json列出资料中的字段类型概况和必要外键；同一字段可能存在null值，类型概况不是严格JSON Schema。后续资料可更正事实，但原记录可按其身份回查。

## 补充领域含义

accepted且kind为withdraw_touch的appeal表示该触点不能再支持这笔订单；missing_code接受后新增的触点仍需处于合同有效窗口。当前归属可以改变，但signed_commissions不被覆盖，recoveries按真实到账区分pending和received。
