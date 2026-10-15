# 何时使用模拟

只在**系统边界**处使用模拟：

- 外部 API(支付、电子邮件等)
- 数据库(有时使用，优先选择测试数据库)
- 时间 / 随机数
- 文件系统(有时使用)

不要模拟：

- 自己的类 / 模块
- 内部协作者
- 任何你能够控制的内容

## 针对模拟的设计

在系统边界处设计易于模拟的接口：

**1. 使用依赖注入**

传入外部依赖，而不是在内部创建：

```typescript
// Easy to mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// Hard to mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 优先采用 SDK 风格的接口，而不是通用获取器**

针对每项外部操作创建具体函数，而不是使用一个带条件逻辑的通用函数：

```typescript
// GOOD: Each function is independently mockable
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// BAD: Mocking requires conditional logic inside the mock
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK 方法意味着：

- 每个模拟都只返回一种特定形状。
- 测试设置中没有条件逻辑。
- 更容易看出测试覆盖了哪些端点。
- 每个端点都具有类型安全性。
