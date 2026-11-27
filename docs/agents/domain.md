# 领域文档

约定工程技能在探索代码库时如何消费本仓库的领域文档。

## 探索之前先读这些

- 根目录的 **`GLOSSARY.md`**，或
- 根目录的 **`GLOSSARY-MAP.md`**（若存在）：它指向每个上下文各自的 `GLOSSARY.md`，读与当前主题相关的那些。
- **`docs/adr/`**：读与即将改动的区域相关的 ADR。多上下文仓库还要看 `src/<context>/docs/adr/` 里的上下文级决策。

这些文件不存在时**静默跳过**。不要提示缺失，也不要一上来就提议创建。`/domain-modeling` 技能（经由 `/grill-with-docs` 和 `/improve-codebase-architecture` 触达）会在术语或决策真正被确定时按需创建。

## 文件结构

单上下文仓库（大多数仓库）：

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文仓库（根目录存在 `GLOSSARY-MAP.md`）：

```
/
├── GLOSSARY-MAP.md
├── docs/adr/                          ← 系统级决策
└── src/
    ├── ordering/
    │   ├── GLOSSARY.md
    │   └── docs/adr/                  ← 上下文级决策
    └── billing/
        ├── GLOSSARY.md
        └── docs/adr/
```

## 使用术语表里的词汇

输出里出现领域概念时（issue 标题、重构方案、假设、测试名），用 `GLOSSARY.md` 定义的那个词，不要漂移到术语表明确回避的同义词。

如果需要的概念还不在术语表里，这是个信号：要么你在发明项目并不使用的语言（重新考虑），要么存在真实缺口（记下来交给 `/domain-modeling`）。

## 标记 ADR 冲突

如果输出与现有 ADR 矛盾，显式指出，而不是悄悄覆盖：

> _与 ADR-0007（事件溯源订单）冲突，但值得重新讨论，因为……_
