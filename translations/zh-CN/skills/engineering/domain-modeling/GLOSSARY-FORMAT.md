# GLOSSARY.md 格式

## 结构

```md
# {上下文名称}

{用一两句话说明这个上下文是什么以及它为什么存在。}

## 术语

**Order**：
{用一两句话描述该术语}
_避免使用_：Purchase, transaction

**Invoice**：
向客户交付商品或服务后发送的付款请求。
_避免使用_：Bill, payment request

**Customer**：
下单的个人或组织。
_避免使用_：Client, buyer, account
```

## 规则

- **明确表达立场。**同一概念有多个词语时，选出最合适的词，并在 `_避免使用_` 下列出其他词语。
- **定义要简洁。**最多一两句话。定义它是什么，而不是它做什么。
- **只包含本项目上下文特有的术语。**通用编程概念（超时、错误类型、实用程序模式）不应收录，即使项目大量使用它们也一样。添加术语前先问：这是本上下文独有的概念，还是通用编程概念？只有前者应该收录。
- **在自然形成的类别下分组术语。**如果所有术语都属于同一个连贯领域，使用平面列表即可。

## 单一上下文与多上下文仓库

**单一上下文（大多数仓库）：**在仓库根目录放置一个 `GLOSSARY.md`。

**多个上下文：**在仓库根目录放置 `GLOSSARY-MAP.md`，列出各个上下文、它们所在位置以及彼此之间的关系：

```md
# Glossary Map

## Contexts

- [Ordering](./src/ordering/GLOSSARY.md): 接收并跟踪客户订单
- [Billing](./src/billing/GLOSSARY.md): 生成发票并处理付款
- [Fulfillment](./src/fulfillment/GLOSSARY.md): 管理仓库拣货和发货

## Relationships

- **Ordering → Fulfillment**: Ordering 发出 `OrderPlaced` 事件；Fulfillment 接收这些事件并开始拣货
- **Fulfillment → Billing**: Fulfillment 发出 `ShipmentDispatched` 事件；Billing 接收这些事件并生成发票
- **Ordering ↔ Billing**: 共享 `CustomerId` 和 `Money` 类型
```

此技能会推断适用的结构：

- 如果存在 `GLOSSARY-MAP.md`，读取它以查找上下文
- 如果仅存在根目录 `GLOSSARY.md`，则按单一上下文处理
- 如果两者都不存在，则在第一个术语得到确认时按需创建根目录 `GLOSSARY.md`

存在多个上下文时，推断当前主题与哪个上下文有关。如果不清楚，就询问。
