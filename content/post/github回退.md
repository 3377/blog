---
# 基本信息 - 必填项
title: github回退 # 文章标题，会自动填入文件名
date: "2025-03-30 23:04:01" # 发布日期，使用Obsidian日期格式
draft: false # 是否为草稿，true表示草稿不会在生产环境发布，false表示正式发布

# 分类与标签 - 可选但推荐填写
tags:
  - github
  - obsidian
  - 教程
#categories: # 文章分类，层级高于标签
#  - { { VALUE:分类 } }

# 布局控制 - 可选
showTableOfContents: true # 是否显示右侧目录导航，true开启，false关闭
showComments: false # 是否显示评论区域，true开启，false关闭
showDate: true # 是否显示文章日期
showDateUpdated: true # 是否显示文章更新日期
showAuthor: true # 是否显示作者信息
showBreadcrumbs: true # 是否显示面包屑导航
showReadingTime: true # 是否显示阅读时间
showWordCount: true # 是否显示字数统计

# 文章元信息 - 可选
#description: "测试" # 文章描述，会显示在搜索引擎结果中
summary: "关于github回退的几种方式" # 自定义摘要，如不填写则自动截取文章开头

# 显示控制 - 可选
hideInList: false # 是否在文章列表中隐藏
isTop: false # 是否置顶文章

# 高级选项 - 可选
#slug: "github" # 自定义URL路径，不填则使用标题生成
toc: true # 目录，同showTableOfContents
katex: false # 是否启用KaTeX数学公式渲染
markup: "md" # 标记语言，支持md、html等
images: # 文章图片，第一张会用作社交媒体分享时的预览图
  - /img/cover.jpg
---

## 🤖 硬回退-不带历史

**目标:** 将本地和远程仓库（假设是 `main` 分支）都强制回退到 `0100870311c7c11e1f793a426bca3d6ea79cbd84` 这个提交，并丢弃之后的所有历史。

**请在你的项目目录下打开 Git Bash 或终端，执行以下命令：**

1.  **确保你在要操作的分支上 (假设是 `main`)：**

    ```
    git checkout main
    ```

    _(如果你的主分支是 `master`，请替换 `main` 为 `master`)_

2.  **硬重置本地分支到目标提交：**

    ```bash
    git reset --hard 0100870311c7c11e1f793a426bca3d6ea79cbd84
    ```

    - **警告:** 这个命令会丢弃你本地 `main` 分支上在 `0100870` 之后的所有提交，并且会**清空你工作区和暂存区中所有未提交的更改**。请确保没有需要保留的未提交内容。

3.  **强制推送到远程仓库：**
    ```bash
    git push --force origin main
    ```
    - _(同样，如果你的主分支是 `master`，请替换 `main`)_
    - **警告:** 这个命令会**强制覆盖**远程 `origin` 仓库的 `main` 分支历史。如果其他人也基于这个分支工作，他们的历史也会被覆盖，可能导致他们丢失工作。请确保这是你想要的结果，并且已经和协作者沟通好（如果需要）。

**总结关键点:**

- `git reset --hard <commit-hash>`: 将**本地**分支和工作区强制设置为指定提交的状态。
- `git push --force origin <branch-name>`: 将**本地**分支的状态强制推送到**远程**分支，覆盖远程历史。

执行完这些命令后，你的本地和远程 `main` 分支都应该指向 `0100870` 这个提交了。

## 🤖 软回退-带历史

好的，如果你想**撤销**后续提交所带来的**更改**，但**保留**这些提交的**历史记录**（即不删除它们，而是添加一个新的提交来抵消它们的效果），你应该使用 `git revert` 命令。

这种方式更安全，尤其是在多人协作的分支上，因为它不会改变已有的提交历史，而是添加新的提交来达到回退的效果。

**目标:** 创建一个新的提交，这个新提交的内容状态与 `0100870311c7c11e1f793a426bca3d6ea79cbd84` 这个提交一致，但保留 `0100870` 之后的所有提交记录。

**请在你的项目目录下打开 Git Bash 或终端，执行以下命令：**

1.  **确保你在要操作的分支上 (假设是 `main`)：**

    ```bash
    git checkout main
    ```

    _(如果你的主分支是 `master`，请替换 `main` 为 `master`)_

2.  **找出需要 revert 的提交范围：**
    我们需要 revert 从 `0100870` (不包括) 到当前 `main` 分支顶端 (包括) 的所有提交。

3.  **执行 revert 操作：**

    ```bash
    git revert --no-commit 0100870..HEAD
    ```

    - `0100870..HEAD`: 这个范围表示从 `0100870` 提交之后（不包含 `0100870` 本身）一直到 `HEAD`（当前分支的最新提交）的所有提交。
    - `--no-commit`: 这个选项很重要。它会计算出撤销这一系列提交所需的所有更改，并将这些更改应用到你的工作区和暂存区，但**不会自动创建**一个或多个 revert commit。这允许我们将所有撤销操作合并到一个单独的提交中。

4.  **检查状态并解决冲突 (如果需要)：**

    - 运行 `git status`。你会看到很多文件被修改了（这些就是 revert 操作带来的反向更改）。
    - **重要:** 如果在 revert 过程中出现冲突 (git 会提示)，你需要手动解决这些冲突。编辑冲突文件，移除 `<<<<<<<`, `=======`, `>>>>>>>` 标记，保留你想要的内容。解决后，使用 `git add <冲突文件>` 将它们标记为已解决。

5.  **创建总的 Revert 提交：**

    - 在所有更改都已暂存（并且冲突已解决）后，创建一个新的提交来记录这次"回退"操作：
      ```bash
      git commit -m "Revert to state of commit 0100870"
      ```
      - 你可以修改引号内的提交信息，使其更清晰地描述这次操作。

6.  **推送到远程仓库：**
    ```bash
    git push origin main
    ```
    - _(同样，如果你的主分支是 `master`，请替换 `main`)_
    - 这次**不需要** `--force`，因为我们是添加了一个新的提交，而不是改写历史。这是一个常规的推送。

**总结关键点:**

- `git revert <commit-range>`: 创建新的提交来**抵消**指定范围内提交所做的更改。
- `--no-commit`: 阻止 `git revert` 自动为每个被 revert 的提交创建单独的 commit，允许我们将所有更改合并到一个 commit 中。
- **保留历史:** 原始的提交（`0100870` 之后的那些）仍然存在于历史记录中，但它们引入的更改被新创建的 revert commit 抵消了。
- **安全推送:** 使用常规的 `git push`，对协作者更友好。

执行完这些命令后，你的本地和远程 `main` 分支的文件状态将回到 `0100870` 时的状态，但 Git 历史中会包含 `0100870` 之后的所有提交以及一个最终的 revert 提交。


## ✨修改提交默认账号

==默认使用某个账号提交某个仓库==

```
git config --global credential.https://github.com/3377/cf-rss.git.username 3377
```
