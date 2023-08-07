# Git提交规范

## 摘要

Git是一种广泛使用的版本控制系统，用于协作开发和管理代码。为了保持代码库的整洁和可维护性，我们需要制定一套规范化的Git提交规范。本文介绍了一套通用的Git提交规范，以帮助团队成员更好地管理和追踪代码变更。

# 规范的作用

大多数情况下，看提交历史的人跟提交代码的人都不是同一个人，当别人阅读你的提交历史时，他很可能是不知道具体代码细节的，你如何在最短的时间内让他一眼知道每次提交的意义：

- 每次提交影响的具体范围？
- 这个bug在哪次提交中被修复了？
- 这个新功能是在哪次提交中增加的？
- 修改是否向下兼容？
- 是否回滚了代码？
- 是否只是修改了文档、调整了代码格式？
- 是否修改了测试、是否进行了重构？
- 是否对代码进行了性能优化？

这些都是提交规范的作用。

# 提交消息的结构

每个Git提交消息应包含三个部分：类型、范围和描述。消息结构的格式如下：

```
<类型>(<范围>): <描述>
```

- 类型
  - **feat：**提交新功能
  - **fix**：修复了bug
  - **docs**：只修改了文档
  - **style**：调整代码格式，未修改代码逻辑（比如修改空格、格式化、缺少分号等）
  - **refactor**：代码重构，既没修复bug也没有添加新功能
  - **perf**：性能优化，提高性能的代码更改
  - **test**：添加或修改代码测试
  - **chore**：对构建流程或辅助工具和依赖库（如文档生成等）的更改

- 范围：表示提交的影响范围，可以是一个文件、一个模块或者一个功能点。
- 描述：简明扼要地描述本次提交的目的和内容。是对变更的简明描述：
  - 使用祈使句，现在时态：是“change”不是“changed”也不是“changes”
  - 不要大写首字母
  - 结尾不要使用句号

示例：

```
feat(user): 添加用户注册功能
fix(bug): 修复登录页面样式问题
docs(readme): 更新README文件
```

1. 提交消息的内容：
   提交消息应该清晰、简明地描述本次提交的目的和内容。避免使用模糊的词汇或过于复杂的句子，使得其他团队成员可以迅速理解本次提交的意图。
2. 提交的频率：
   为了保持代码库的可读性和可维护性，建议团队成员进行小而频繁的提交，而不是长时间的开发后一次性提交大量代码。频繁的提交可以更好地追踪代码变更，减少合并冲突的可能性。
3. 提交的顺序：
   推荐按照功能模块或者文件的逻辑顺序进行提交，这样可以更好地组织代码历史记录，方便查找和回溯。
4. 分支管理：
   在团队协作开发中，建议使用分支管理代码。每个功能或修复应该在自己的分支上进行开发，开发完成后再合并到主分支。这样可以保持主分支的稳定性，并且方便追踪每个功能或修复的历史记录。

结论：
通过制定和遵守Git提交规范，可以提高团队协作的效率和代码库的可维护性。规范的提交消息能够更好地记录代码变更，方便团队成员进行代码审查、追溯和维护。同时，合理的分支管理也能够保持代码库的稳定性和可追踪性。让我们共同遵守Git提交规范，为项目的成功和可持续发展做出贡献。

## Quick Start

### 全局安装commitizen & cz-conventional-changelog

commitizen是一个撰写合格commit message的工具，用于代替git commit 指令，而cz-conventional-changelog适配器提供conventional-changelog标准（约定式提交标准）。基于不同需求，也可以使用不同适配器。

```text
npm install -g commitizen cz-conventional-changelog
echo '{ "path": "cz-conventional-changelog" }' > ~/.czrc
```

安装完毕后，可直接使用git cz来取代git commit。

全局模式下，需要 ~/.czrc 配置文件, 为commitizen指定Adapter。

### 项目内安装changelog

conventional-changelog-cli和cz-conventional-changelog是两个与Git提交规范相关的工具。

#### conventional-changelog-cli：

conventional-changelog-cli是一个命令行工具，用于根据Git提交规范自动生成变更日志（changelog）。它可以根据项目的提交历史生成标准化的changelog，包括每个版本的新增功能、修复的Bug和其他变更等信息。它支持多种输出格式，如Markdown、JSON等，可以根据项目需求进行定制。

使用conventional-changelog-cli，你可以在每次发布新版本时运行它，自动生成更新的changelog。这样可以帮助团队和用户了解每个版本的变更内容，方便追踪和理解项目的发展。

#### cz-conventional-changelog：

cz-conventional-changelog是一个用于生成符合Git提交规范的交互式命令行工具。它可以帮助开发者在提交代码时选择和填写规范化的提交消息，而不需要手动输入每个字段。cz-conventional-changelog提供了一组预定义的问题，引导开发者填写类型、范围和描述等提交消息的各个部分。

通过使用cz-conventional-changelog，开发者可以更加方便地遵循Git提交规范，减少提交消息格式错误的可能性，并且提高提交消息的一致性和可读性。

这两个工具的结合使用可以帮助团队更好地遵循和应用Git提交规范。conventional-changelog-cli用于自动生成规范化的changelog，而cz-conventional-changelog则提供了交互式的提交消息生成方式，简化了提交过程。它们可以与Git的钩子（hooks）结合使用，使得规范的提交成为团队开发流程的一部分，提高团队协作和代码库的可维护性。

- `npm install -g conventional-changelog-cli`
- `npm install -g cz-conventional-changelog`

### 使用

执行git cz进入interactive模式，根据提示依次填写

```text
1.Select the type of change that you're committing 选择改动类型 (<type>)

2.What is the scope of this change (e.g. component or file name)? 填写改动范围 (<scope>)

3.Write a short, imperative tense description of the change: 写一个精简的描述 (<subject>)

4.Provide a longer description of the change: (press enter to skip) 对于改动写一段长描述 (<body>)

5.Are there any breaking changes? (y/n) 是破坏性修改吗？默认n (<footer>)

6.Does this change affect any openreve issues? (y/n) 改动修复了哪个问题？默认n (<footer>)
```

生成的commit message格式如下：

```text
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

填写完毕后，husky会调用commitlint对message进行格式校验，默认规定type及subject为必填项。

任何git commit指令的option都能用在 git cz指令上, 例如git cz -a

使用`conventional-changelog -p angular -i CHANGELOG.md -s`可以生成changelog文件

参考资料：

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git分支管理策略](https://nvie.com/posts/a-successful-git-branching-model/)