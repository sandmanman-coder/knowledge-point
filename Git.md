# Git 团队协作工作流教程

本教程将手把手教你如何在深度学习项目中使用 Git 进行良好的代码管理和团队协作。

## 目录

1. [Git 基础配置](#1-git-基础配置)
2. [初始化项目仓库](#2-初始化项目仓库)
3. [分支管理策略](#3-分支管理策略)
4. [日常开发工作流](#4-日常开发工作流)
5. [提交规范](#5-提交规范)
6. [代码审查流程](#6-代码审查流程)
7. [冲突解决](#7-冲突解决)
8. [最佳实践](#8-最佳实践)
9. [常见问题](#9-常见问题)

---

## 1. Git 基础配置

### 1.1 安装 Git

```bash
# Ubuntu/Debian
sudo apt-get install git

# CentOS/RHEL
sudo yum install git

# macOS (使用 Homebrew)
brew install git
```

### 1.2 配置用户信息

```bash
# 设置用户名
git config --global user.name "你的名字"

# 设置邮箱
git config --global user.email "your.email@example.com"

# 查看配置
git config --list
```

### 1.3 配置 Git 编辑器和别名

```bash
# 设置默认编辑器为 vim
git config --global core.editor vim

# 设置常用别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

### 1.4 配置 SSH 密钥 (推荐)

```bash
# 生成 SSH 密钥
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# 查看公钥
cat ~/.ssh/id_rsa.pub

# 将公钥添加到 GitHub/GitLab
# 复制输出的内容到 GitHub Settings -> SSH Keys
```

---

## 2. 初始化项目仓库

### 2.1 初始化本地仓库

```bash
# 进入项目目录
cd /home/bubble/桌面/git_test

# 初始化 Git 仓库
git init

# 查看状态
git status
```

### 2.2 添加并提交初始文件

```bash
# 添加所有文件到暂存区
git add .

# 查看将要提交的内容
git status

# 提交初始代码
git commit -m "chore: initial project setup with basic structure"
```

### 2.3 连接远程仓库

```bash
# 添加远程仓库 (以 GitHub 为例)
git remote add origin git@github.com:username/project-name.git

# 或使用 HTTPS
git remote add origin https://github.com/username/project-name.git

# 查看远程仓库
git remote -v

# 推送到远程仓库
git push -u origin main
```

### 2.4 如果远程仓库已存在

```bash
# 克隆远程仓库
git clone git@github.com:username/project-name.git

# 进入项目目录
cd project-name
```

---

## 3. 分支管理策略

我们采用 **Git Flow** 工作流，包含以下分支类型：

### 3.1 主要分支

- **`main`** (或 `master`): 生产环境分支，始终保持稳定
- **`develop`**: 开发分支，集成最新的开发功能

### 3.2 辅助分支

- **`feature/*`**: 功能分支，用于开发新功能
- **`bugfix/*`**: 修复分支，用于修复开发中的bug
- **`hotfix/*`**: 热修复分支，用于紧急修复生产环境bug
- **`release/*`**: 发布分支，用于准备新版本发布

### 3.3 创建开发分支

```bash
# 从 main 分支创建 develop 分支
git checkout -b develop main

# 推送到远程
git push -u origin develop
```

### 3.4 分支命名规范

```
feature/add-data-augmentation      # 添加数据增强功能
feature/implement-resnet           # 实现 ResNet 模型
bugfix/fix-training-loss           # 修复训练损失计算错误
hotfix/fix-memory-leak             # 修复内存泄漏
release/v1.0.0                     # 版本 1.0.0 发布
```

---

## 4. 日常开发工作流

### 4.1 开始新功能开发

```bash
# 1. 确保 develop 分支是最新的
git checkout develop
git pull origin develop

# 2. 创建功能分支
git checkout -b feature/add-resnet-model

# 3. 进行开发工作
# 编辑文件...

# 4. 查看修改
git status
git diff

# 5. 添加修改到暂存区
git add src/models/resnet.py
# 或添加所有修改
git add .

# 6. 提交修改
git commit -m "feat: add ResNet-50 model implementation"

# 7. 推送到远程
git push -u origin feature/add-resnet-model
```

### 4.2 持续开发过程

```bash
# 定期同步 develop 分支的更新
git checkout develop
git pull origin develop

# 切回功能分支
git checkout feature/add-resnet-model

# 合并 develop 的更新到当前分支
git merge develop

# 解决可能的冲突后继续开发
```

### 4.3 完成功能开发

```bash
# 1. 确保所有改动已提交
git status

# 2. 同步最新的 develop 分支
git checkout develop
git pull origin develop

# 3. 切回功能分支并合并 develop
git checkout feature/add-resnet-model
git merge develop

# 4. 推送到远程
git push origin feature/add-resnet-model

# 5. 在 GitHub/GitLab 上创建 Pull Request (PR) / Merge Request (MR)
# 目标分支: develop
# 源分支: feature/add-resnet-model
```

### 4.4 合并后清理

```bash
# PR 被合并后，删除本地分支
git checkout develop
git pull origin develop
git branch -d feature/add-resnet-model

# 删除远程分支
git push origin --delete feature/add-resnet-model
```

---

## 5. 提交规范

我们采用 **Conventional Commits** 规范，使提交历史更清晰。

### 5.1 提交消息格式

```
<type>(<scope>): <subject>

<body>

<footer>
```

### 5.2 Type 类型

- **feat**: 新功能
- **fix**: 修复 bug
- **docs**: 文档更新
- **style**: 代码格式调整（不影响代码运行）
- **refactor**: 重构（既不是新功能也不是bug修复）
- **perf**: 性能优化
- **test**: 添加或修改测试
- **chore**: 构建过程或辅助工具的变动
- **ci**: CI/CD 配置文件和脚本的变动

### 5.3 提交示例

```bash
# 新功能
git commit -m "feat(model): add ResNet-50 architecture"

# 修复bug
git commit -m "fix(training): correct learning rate scheduler behavior"

# 文档
git commit -m "docs: update README with installation instructions"

# 重构
git commit -m "refactor(data): reorganize dataloader module structure"

# 性能优化
git commit -m "perf(model): optimize batch processing speed"

# 多行提交消息
git commit -m "feat(training): add mixed precision training

- Implement automatic mixed precision using torch.cuda.amp
- Add gradient scaling to prevent underflow
- Update training configuration with amp settings

Closes #123"
```

### 5.4 提交最佳实践

1. **单一职责**: 每个提交只做一件事
2. **原子性**: 提交应该是完整的、可运行的
3. **频繁提交**: 小步提交，便于回滚和审查
4. **有意义的消息**: 清晰描述改动内容和原因

```bash
# ❌ 不好的提交
git commit -m "update"
git commit -m "fix bug"
git commit -m "修改了一些东西"

# ✅ 好的提交
git commit -m "feat(model): implement attention mechanism in CNN"
git commit -m "fix(data): resolve image preprocessing dimension mismatch"
git commit -m "refactor(utils): extract metrics calculation into separate module"
```

---

## 6. 代码审查流程

### 6.1 创建 Pull Request

1. 推送功能分支到远程
2. 在 GitHub/GitLab 上点击 "New Pull Request"
3. 选择目标分支（通常是 `develop`）
4. 填写 PR 描述：
   - 功能说明
   - 改动内容
   - 测试情况
   - 相关 Issue

### 6.2 PR 描述模板

```markdown
## 改动描述
简要描述本次 PR 的主要改动内容

## 改动类型
- [ ] 新功能
- [ ] Bug 修复
- [ ] 文档更新
- [ ] 代码重构
- [ ] 性能优化

## 改动内容
- 实现了 ResNet-50 模型
- 添加了预训练权重加载功能
- 更新了训练配置文件

## 测试情况
- [ ] 已通过本地测试
- [ ] 已添加单元测试
- [ ] 已在 CIFAR-10 数据集上验证

## 相关 Issue
Closes #123

## 截图（如适用）
```

### 6.3 代码审查清单

审查者应该检查：

- ✅ 代码逻辑是否正确
- ✅ 是否符合项目编码规范
- ✅ 是否有适当的注释和文档
- ✅ 是否有必要的测试
- ✅ 是否有潜在的性能问题
- ✅ 是否有安全隐患

### 6.4 响应审查意见

```bash
# 根据审查意见修改代码
# 编辑文件...

# 提交修改
git add .
git commit -m "refactor: address PR review comments"

# 推送更新
git push origin feature/add-resnet-model
```

---

## 7. 冲突解决

### 7.1 识别冲突

```bash
# 尝试合并时出现冲突
git merge develop

# Git 会提示：
# CONFLICT (content): Merge conflict in src/models/model.py
# Automatic merge failed; fix conflicts and then commit the result.
```

### 7.2 查看冲突文件

```bash
# 查看冲突状态
git status

# 查看冲突内容
git diff
```

冲突标记示例：
```python
<<<<<<< HEAD
# 你的改动
model = SimpleCNN(num_classes=10)
=======
# develop 分支的改动
model = SimpleCNN(num_classes=100)
>>>>>>> develop
```

### 7.3 解决冲突

```bash
# 1. 手动编辑冲突文件，选择保留的内容
# 删除冲突标记，保留正确的代码

# 2. 标记冲突已解决
git add src/models/model.py

# 3. 完成合并
git commit -m "merge: resolve conflicts with develop branch"
```

### 7.4 使用合并工具

```bash
# 使用可视化合并工具
git mergetool

# 配置 VS Code 作为合并工具
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

### 7.5 预防冲突

1. 频繁同步 develop 分支
2. 及时合并上游更新
3. 与团队沟通避免同时修改相同文件
4. 保持功能分支短小

---

## 8. 最佳实践

### 8.1 提交前检查

```bash
# 查看将要提交的内容
git diff --staged

# 运行测试
python -m pytest tests/

# 代码格式化
black src/
isort src/

# 检查代码质量
flake8 src/
```

### 8.2 使用 .gitignore

确保不提交不必要的文件：

```bash
# 查看被忽略的文件
git status --ignored

# 检查某个文件是否被忽略
git check-ignore -v filename
```

### 8.3 保护重要分支

在 GitHub/GitLab 上设置分支保护规则：

- 禁止直接推送到 `main` 和 `develop`
- 要求 PR 审查
- 要求 CI 通过
- 要求代码审查通过

### 8.4 使用标签管理版本

```bash
# 创建标签
git tag -a v1.0.0 -m "Release version 1.0.0"

# 推送标签到远程
git push origin v1.0.0

# 推送所有标签
git push origin --tags

# 查看所有标签
git tag -l

# 检出特定版本
git checkout v1.0.0
```

### 8.5 编写有意义的 README

README.md 应该包含：
- 项目简介
- 安装说明
- 使用方法
- 示例代码
- 贡献指南
- 许可证信息

### 8.6 使用 Git Hooks

创建 `.git/hooks/pre-commit` 钩子：

```bash
#!/bin/bash
# 提交前自动格式化代码

echo "Running pre-commit hooks..."

# 格式化代码
black src/
isort src/

# 添加格式化后的文件
git add -u

echo "Pre-commit hooks completed!"
```

---

## 9. 常见问题

### 9.1 撤销操作

```bash
# 撤销工作区的修改
git checkout -- filename

# 撤销暂存区的文件
git reset HEAD filename

# 修改最后一次提交
git commit --amend

# 回退到上一个提交（保留改动）
git reset --soft HEAD^

# 回退到上一个提交（丢弃改动）
git reset --hard HEAD^

# 回退到指定提交
git reset --hard commit_hash
```

### 9.2 查看历史

```bash
# 查看提交历史
git log

# 简洁模式
git log --oneline

# 图形化显示
git log --graph --oneline --all

# 查看某个文件的历史
git log --follow filename

# 查看某次提交的详细信息
git show commit_hash
```

### 9.3 暂存工作

```bash
# 暂存当前工作
git stash

# 查看暂存列表
git stash list

# 恢复暂存的工作
git stash pop

# 恢复特定的暂存
git stash apply stash@{0}

# 删除暂存
git stash drop stash@{0}
```

### 9.4 比较差异

```bash
# 比较工作区和暂存区
git diff

# 比较暂存区和最后一次提交
git diff --staged

# 比较两个分支
git diff develop..feature/new-feature

# 比较两个提交
git diff commit1 commit2
```

### 9.5 远程操作

```bash
# 查看远程仓库信息
git remote show origin

# 拉取远程分支
git fetch origin

# 拉取并合并
git pull origin develop

# 强制推送（谨慎使用）
git push -f origin feature-branch

# 清理本地已删除的远程分支引用
git remote prune origin
```

---

## 快速参考

### 日常使用速查表

```bash
# 1. 开始新功能
git checkout develop
git pull origin develop
git checkout -b feature/new-feature

# 2. 开发过程
git add .
git commit -m "feat: add new feature"
git push origin feature/new-feature

# 3. 同步主分支
git checkout develop
git pull origin develop
git checkout feature/new-feature
git merge develop

# 4. 完成开发
# 在 GitHub/GitLab 创建 PR

# 5. 清理分支
git checkout develop
git pull origin develop
git branch -d feature/new-feature
```

### 紧急修复流程

```bash
# 1. 从 main 创建 hotfix 分支
git checkout main
git pull origin main
git checkout -b hotfix/critical-bug

# 2. 修复并测试
# 编辑代码...
git add .
git commit -m "fix: resolve critical production bug"

# 3. 合并到 main 和 develop
git checkout main
git merge hotfix/critical-bug
git push origin main

git checkout develop
git merge hotfix/critical-bug
git push origin develop

# 4. 打标签
git tag -a v1.0.1 -m "Hotfix release 1.0.1"
git push origin v1.0.1

# 5. 删除 hotfix 分支
git branch -d hotfix/critical-bug
```

---

## 团队协作规则

1. **永远不要直接推送到 main 或 develop**
2. **所有功能通过 PR 合并**
3. **PR 需要至少一人审查**
4. **保持提交历史清晰**
5. **及时同步上游更新**
6. **遵循提交规范**
7. **写清晰的 PR 描述**
8. **积极参与代码审查**

---

## 学习资源

- [Pro Git 书籍](https://git-scm.com/book/zh/v2)
- [Git 官方文档](https://git-scm.com/doc)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Flow 工作流](https://nvie.com/posts/a-successful-git-branching-model/)

---

**祝你使用 Git 愉快！有问题随时查阅本文档。** 🚀
