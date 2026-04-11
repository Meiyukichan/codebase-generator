# Codebase Generator — 大型项目文档树生成与查询系统

为大型代码库（50万行代码以上）生成分层结构化文档树，并提供基于文档树的功能查询能力。

**输出是中文。**

---

## 组件概览

| 组件 | 说明 |
|------|------|
| **codebase-generator** | 生成器，为大型项目生成分层结构化文档树 |
| **codebase-explorer** | 查询器，在已有文档树中按业务/功能描述定位相关实现 |
| **codebases** | 子代理，供主代理调用 explorer |

---

## 工作流程

```
源码项目
    │
    ▼
┌─────────────────────────────────┐
│     codebase-generator          │
│     生成文档树                   │
│  （toc.md + spec 文档）          │
└─────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────┐
│     codebase-explorer           │
│     查询文档树                   │
│  （定位 spec + 源码片段）         │
└─────────────────────────────────┘
```

---

## 目录结构

```
skills/codebase-generator/
├── README.md                        ← 本文件
├── agents/
│   └── codebases.md                 ← 子代理定义
├── references/
│   └── SPEC-GENERATION-GUIDE.md     ← spec 文档生成规范
└── skills/
    ├── codebase-generator/
    │   └── SKILL.md                 ← 生成器 skill
    └── codebase-explorer/
        └── SKILL.md                 ← 查询器 skill
```

---

## 文档树结构

```
{文档树路径}/
├── toc.md                          ← 总纲
├── {模块-a}/
│   ├── toc.md                      ← 模块索引
│   ├── {子模块-1}/
│   │   ├── toc.md                  ← 子模块索引
│   │   ├── spec-xxx.md
│   │   └── spec-yyy.md
│   └── spec-zzz.md                 ← 直接挂在模块下的spec
├── {模块-b}/
│   ├── toc.md
│   └── ...
└── ...
```

| 层级 | 说明 |
|------|------|
| **总纲** | 项目级索引，检索器（retriever）查询的入口 |
| **模块** | 大领域聚集（如工具模块、插件模块） |
| **子模块** | 小领域聚集 |
| **spec 文档** | 最小粒度，对应具体功能/接口的详细规格说明 |

---

## 生成器 — codebase-generator

### 功能

根据源代码自动生成分层结构的项目文档，包含：
- 各级索引文件（toc.md）
- 详细的 spec 文档

### 输入

| 参数 | 说明 |
|------|------|
| **项目路径** | 待分析的源代码根目录 |
| **文档树路径** | 生成的文档树输出目录 |

### 约束

- 索引文件（toc.md）≤ 500 行
- 模块下子项（子模块 + 直接spec）总数 ≤ 30
- 子模块包含 5~40 个 spec 文档

---

## 查询器 — codebase-explorer

### 功能

在已有文档树中，按业务/功能描述逐层导航，定位到相关 spec 文档和源码实现。

### 输入

| 参数 | 说明 |
|------|------|
| **文档树路径** | codebase-generator 生成的文档树根目录 |
| **查询描述** | 要查找的业务、功能或代码模块的简要描述 |

### 导航路径

```
总纲 → 模块索引 → 子模块索引 → spec文档 → 源码
```

### 输出

- 匹配的 spec 文档摘要
- 核心源码片段（含上下游追溯）

---

## 子代理 — codebases

供主代理调用，使用时必须明确指定：

- 中文触发词：`使用codebases子代理`、`@"codebases (agent)"`、`通过codebases subagent`
- 英文触发词：`Use the codebases subagent`

---

## 相关资料

- [生成器 SKILL.md](./skills/codebase-generator/SKILL.md)
- [查询器 SKILL.md](./skills/codebase-explorer/SKILL.md)
- [SPEC 生成规范](./references/SPEC-GENERATION-GUIDE.md)
