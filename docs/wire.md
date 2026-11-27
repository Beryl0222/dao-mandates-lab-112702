# 数据契约

治理票数为整数。快照采用指定hash对应区块结束时状态，委托可以传递，环中的票权不能参与。法定人数按有效yes/no/abstain总和，赞成比例按yes/(yes+no)，分母为零不通过。投票最后一笔有效选择优先，截止后不接受更改。默认投票截止为快照后10块，执行时锁2块，最终性为3块后显式finalize_chain事实；分支冲突仍需展示异常，不把已支付冲销。里程碑0无附加条件，后续里程碑需独立批准。priority小者先获得资金，未承诺不等于已付款。guardian只能暂停，AR1类仲裁身份不等于治理投票权。

session.json 的 entities 为初始登记资料，events 为按 sequence 送达的事实或用户请求。observedAt 是导入观察时间；业务生效时点取载荷中的 date、at、effective、block 等字段，不因数组位置改变这些时点。type 标识记录类别，payload 保留原始业务字段；请求不是已经发生的结果。例中回报用可读 instruction、request、trade、group 等关联键；实际执行可为请求生成稳定身份并在查询中返回映射。

金额和数量采用十进制整数字符串；业务协议明确的小数率、价格和风险值使用十进制字符串，不使用二进制浮点隐式改写输入。Bps为万分比，时间为UTC；未提供业务日期的报价在观察日生效。status中的pending或未知不等于失败，显式的done/paid/settled/accepted依其事件种类解释。

事件种类与载荷字段：
- block：hash, height, parent。
- delegate：block, owner, to。
- vote：block, choice, proposal, voter。
- finalize_chain：hash, height。
- execution_request：installment, proposal。
- payment_report：installment, proposal, status, units。
- pause：actor, scope。
- milestone：approved, index, proposal。
- branch_change：removed, replacement。

这些输入可复制并修改为新的独立会话；它们没有预存成交分配、决策结果或业务账目。

演练签名采用Ed25519，对envelope逐层按键名升序排列的无空白JSON UTF-8签名。signatures.json提供只用于虚构身份的密钥与一条有效编码，实际业务载荷应使用对应角色和权限；样例签名不赋予其他业务权限。

alternative.json 是独立会话，改变了一项实际输入事实；不要把两个会话合并成同一业务事件流。
