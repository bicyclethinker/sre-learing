## 日常 Git 流程

每天结束前：

```bash
git status
git diff
git add .
git commit -m "类型: 本次学习成果"
git push
```

推荐提交类型：

```text
notes: 主题笔记
lab: 场景练习
review: 学习复盘
fix: 修正错误知识
docs: 调整文档结构
chore: 仓库维护
script: 新增辅助脚本
```

示例：

```bash
git commit -m "notes: explain directory permissions"
git commit -m "lab: practice shared directory configuration"
git commit -m "fix: correct file deletion permission notes"
git commit -m "review: summarize cycle 03"
```

一个提交代表一个有意义的学习成果。

不要：

```bash
git commit -m "更新"
git commit -m "改了一点"
git commit -m "111"
```

## 分支策略

个人学习仓库前期只使用 `main` 即可。

不要为了“练 Git”强行把每篇笔记放到不同分支。

以下情况再开分支：

- 大规模重构目录
- 编写新的分析脚本
- 尝试自动生成报告
- 修改大量笔记结构

例如：

```bash
git switch -c feature/history-analysis-script
```

## GitHub 功能分工

| 功能       | 用途                   |
| ---------- | ---------------------- |
| Repository | 保存笔记和练习         |
| Commit     | 记录学习成果           |
| Issues     | 保存暂时无法解决的问题 |
| README     | 展示当前学习状态       |
| Releases   | 暂时不需要             |
| Wiki       | 暂时不需要             |
| Projects   | 暂时不需要             |

不要一开始同时学习 GitHub 所有功能。