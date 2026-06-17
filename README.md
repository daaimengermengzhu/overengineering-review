# Overengineering Review

`overengineering-review` 是一个面向 AI 编码助手的只读代码审查插件，专门用来识别“代码是否写得比实际需要更复杂”。

这个仓库同时提供两套分发入口：

- `Codex` 用户：安装 `Codex` 版本
- `Cloud` 用户：安装 `Cloud` 版本

两边不是“同一个安装入口强行兼容”，而是同一个仓库里分别提供各自生态需要的 marketplace 和 manifest。你只需要执行对应的一行命令即可安装。

## 这个插件做什么

这个插件的核心目标不是泛泛点评代码风格，而是找出真正有机会做减法的地方，例如：

- 可以直接删除的代码
- 只有一次使用场景却提前抽象出来的层
- 本来应该直接用标准库或平台能力，却手写了一套的实现
- 为了很小的功能额外引入依赖
- 只是换个名字再包一层的重复 wrapper
- 保持同样行为但可以写得更短、更直接的实现

## 它重点检查什么

插件会围绕几类明确的“可简化信号”来输出 finding：

- `delete`：死代码、猜测性扩展点、没人用的开关或参数
- `stdlib`：语言或运行时已经提供了能力，却又手写了一套
- `native`：本来应交给平台、浏览器、数据库或框架处理的逻辑
- `yagni`：为了未来假想的第二种用法提前加上的抽象，但现实里并没有出现
- `dependency`：为一个很小的事情引入了额外依赖
- `duplication`：wrapper 或 helper 基本只是给另一个 helper 改名
- `shrink`：在不改变行为的前提下，可以明显收缩代码体积和复杂度

## 它不做什么

- 不会自动重写代码。
- 不是通用 bug 扫描器，也不是安全审计工具。
- 不会把合理的复杂度一概算成过度设计。
- 不会把必要的输入校验、无障碍基础、运维可观测性或回归测试误判成“多余复杂度”。

## 一键安装

### Codex 用户

`bash` / `zsh`:

```bash
codex plugin marketplace add https://github.com/daaimengermengzhu/overengineering-review.git --ref main --sparse .agents/plugins --sparse plugins/overengineering-review && codex plugin add overengineering-review@overengineering-review-marketplace
```

`PowerShell`:

```powershell
codex plugin marketplace add https://github.com/daaimengermengzhu/overengineering-review.git --ref main --sparse .agents/plugins --sparse plugins/overengineering-review; if ($?) { codex plugin add overengineering-review@overengineering-review-marketplace }
```

安装后可以直接用：

```text
/overengineering-review:review
/overengineering-review:review src/api
```

### Claude 用户

`bash` / `zsh`:

```bash
claude plugin marketplace add https://github.com/daaimengermengzhu/overengineering-review.git --scope user --sparse .claude-plugin --sparse plugins/overengineering-review && claude plugin install overengineering-review@overengineering-review-marketplace
```

`PowerShell`:

```powershell
claude plugin marketplace add https://github.com/daaimengermengzhu/overengineering-review.git --scope user --sparse .claude-plugin --sparse plugins/overengineering-review; if ($?) { claude plugin install overengineering-review@overengineering-review-marketplace }
```

## 使用示例

安装完成后，可以直接这样发起审查：

- `/overengineering-review:review`
- `/overengineering-review:review src/api`
- `Review this project for over-engineering.`
- `Review the current diff for unnecessary complexity.`
- `Find code we can simplify or delete.`
- `帮我看看这个项目有没有过度设计。`
- `检查一下当前改动里哪些抽象是不必要的。`

## 预期输出格式

典型 finding 会像这样：

```text
src/cache.ts:84 - yagni: CacheAdapter 只有一个实现。改成直接导出模块。Impact: 去掉一层没有必要的间接封装。
src/date.ts:12 - stdlib: 这段自定义 ISO 日期解析重复了运行时内置能力。改用标准解析方式。Impact: 减少维护成本。
```

随后它还会补充几类总结：

- `Net simplification`：大致能删掉、合并掉多少内容
- `Keep as-is`：看起来复杂，但其实有必要保留的部分
- `Fix priority`：最值得先做的 1 到 3 个简化项

如果没有发现明显问题，预期结论会是：

```text
No clear over-engineering found in the reviewed scope.
```

## 仓库结构

这个仓库里同时放了 Codex 和 Claude 两边需要的入口文件：

```text
.
├─ .agents/
│  └─ plugins/
│     └─ marketplace.json
├─ .claude-plugin/
│  └─ marketplace.json
└─ plugins/
   └─ overengineering-review/
      ├─ commands/
      │  └─ review.md
      ├─ .claude-plugin/
      │  └─ plugin.json
      ├─ .codex-plugin/
      │  └─ plugin.json
      └─ skills/
         └─ overengineering-review/
            ├─ SKILL.md
            └─ agents/
               └─ openai.yaml
```

## License

MIT
