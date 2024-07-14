+++
title = 'Git基础命令'
date = 2022-04-08
draft = false
categories = ["学习"]
+++

## 获取 Git 仓库

通常有两种获取 Git 项目仓库的方式：

1. 将尚未进行版本控制的本地目录转换为 Git 仓库；
2. 从其它服务器**克隆**一个已存在的 Git 仓库。

两种方式都会在你的本地机器上得到一个工作就绪的 Git 仓库。

### 在已存在目录中初始化仓库

```bash
git init
```

该命令将创建一个名为` .git`的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是Git 仓库的骨干。 但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪。

### 克隆现有的仓库

克隆仓库的命令是 `git clone <url>`。比如，要克隆 Git 的链接库**libgit2**，可以用下面的命令：

```bash
git clone https://github.com/libgit2/libgit2
```

这会在当前目录下创建一个名为`libgit2`的目录，并在这个目录下初始化一个` .git`文件夹， 从远程仓库拉取下所有数据放入` .git`文件夹，然后从中读取最新版本的文件的拷贝。 如果你进入到这个新建的` libgit2`文件夹，你会发现所有的项目文件已经在里面了，准备就绪等待后续的开发和使用。

如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以通过额外的参数指定新的目录名：

```bash
git clone https://github.com/libgit2/libgit2 mylibgit
```

这会执行与上一条命令相同的操作，但目标目录名变为了`mylibgit`。

## 记录每次更新到仓库

现在我们的机器上有了一个**真实项目**的 Git 仓库，并从这个仓库中检出了所有文件的**工作副本**。 通常，你会对这些文件做些修改，每当完成了一个阶段的目标，想要将记录下它时，就将它提交到仓库。

请记住，你工作目录下的每一个文件都不外乎这两种状态：**已跟踪(tracked)**或**未跟踪(untracked)**。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后， 它们的状态可能是未修改，已修改或已放入暂存区。简而言之，已跟踪的文件就是 Git 已经知道的文件。

工作目录中除已跟踪文件外的其它所有文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有被放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态，因为 Git刚刚检出了它们， 而你尚未编辑过它们。

编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。 在工作时，你可以选择性地将这些修改过的文件放入暂存区，然后提交所有已暂存的修改，如此反复。

![文件的状态变化周期](https://cdn.jsdelivr.net/gh/aahowe/image@main/screen_2022-04-08%2022.25.14.jpg)



### 检查当前文件状态

可以用 `git status`命令查看哪些文件处于什么状态。如果在克隆仓库后立即使用此命令，会看到类似这样的输出：

```bash
$ git status 
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

### 跟踪新文件

使用命令`git add`开始跟踪一个文件。所以，要跟踪`README`文件，运行：

```bash
git add README
```

此时再运行 `git status`命令，会看到`README`文件已被跟踪，并处于暂存状态：

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:  
	(use "git restore --staged <file>..." to unstage)    

		new file:   README
```

只要在`Changes to be committed`这行下面的，就说明是已暂存状态。 如果此时提交，那么该文件在你运 `git add`时的版本将被留存在后续的历史记录中。 你可能会想起之前我们使用 `git init`后就运行了` gitadd <files>`命令，开始跟踪当前目录下的文件。 `git add`命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

### 暂存已修改的文件

现在我们来修改一个已被跟踪的文件。 如果你修改了一个名为` CONTRIBUTING.md`的已被跟踪的文件，然后运行` git status`命令，会看到下面内容：

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)    
	
		new file:   README
		
Changes not staged for commit:
	(use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in workingdirectory)    
  
  	modified:   CONTRIBUTING.md
```

文件` CONTRIBUTING.md`出现在` Changes not staged for commit`这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。 要暂存这次更新，需要运行` git add`命令。 这是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。 将这个命令理解为“精确地将内容添加到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。现在让我们运行` git add`将`CONTRIBUTING.md`放到暂存区。

### 忽略文件

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建一个名为` .gitignore`的文件，列出要忽略的文件的模式。

文件` .gitignore`的格式规范如下：

- 所有空行或者以**#**开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
- 匹配模式可以以（/）开头防止递归。
- 匹配模式可以以（/）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]`匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]`表示匹配所有 0 到 9 的数字）。 使用两个星号（`**`）表示匹配任意中间目录，比如 `a/**/z`可以匹配 `a/z`、 `a/b/z`或 `a/b/c/z`等。

我们看一个 `.gitignore`文件的例子：

```bash
# 忽略所有的 .a 文件
*.a
# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a
# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO
# 忽略任何目录下名为 build 的文件夹
build/
# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt
# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

GitHub 有一个十分详细的针对数十种项目及语言的 `.gitignore`文件列表， 你可以在https://github.com/github/gitignore找到它。

### 查看已暂存和未暂存的修改

要查看尚未暂存的文件更新了哪些部分，不加参数直接输入` git diff`：

```bash
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy. 
 Please include a nice description of your changes when you submit yourPR; 
 if we have to read the whole diff to figure out why you're contributing 
 in the first place, you're less likely to get feedback and have yourchange
-merged in.
+merged in. Also, split your changes into comprehensive chunks if yourpatch is
+longer than a dozen lines. 
If you are starting to work on a particular area, feel free to submit a PR that highlights your work in progress (and note in the PR title that it's
```

若要查看已暂存的将要添加到下次提交里的内容，可以用` git diff --staged`命令。 这条命令将比对已暂存文件与最后一次提交的文件差异。

请注意，`git diff `本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件，运行` git diff`后却什么也没有，就是这个原因。

### 提交更新

现在的暂存区已经准备就绪，可以提交了。 在此之前，请务必确认还有什么已修改或新建的文件还没有` git add`过， 否则提交的时候不会记录这些尚未暂存的变化。 这些已修改但未暂存的文件只会保留在本地磁盘。 所以，每次准备提交前，先用` git status`看下，你所需要的文件是不是都已暂存起来了，然后再运行提交命令`git commit`：

```bash
git commit
```

这样会启动你选择的文本编辑器来输入提交说明。

另外，你也可以在` commit`命令后添加` -m`选项，将提交信息与命令放在同一行，如下所示：

```bash
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed 
2 files changed, 2 insertions(+) 
create mode 100644 README
```

### 跳过使用暂存区域

尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。 Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给` git commit`加上 `-a`选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add`步骤：

```bash
git commit -a
```

### 移除文件

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。可以用` git rm`命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

如果只是简单地从工作目录中手工删除文件，运行 git status时就会在`Changes not staged forcommit`部分（也就是 未暂存清单）看到，然后再运行` git rm`记录此次移除文件的操作，下一次提交时，该文件就不再纳入版本管理了。 如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项` -f`。 这是一种安全特性，用于防止误删尚未添加到快照的数据，这样的数据不能被 Git 恢复。

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 `.gitignore`文件，不小心把一个很大的日志文件或一堆` .a`这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用` --cached`选项：

```bash
git rm --cached 要删除的文件
//git rm命令后面可以列出文件或者目录的名字，也可以使用 glob模式。
```

### 移动文件

要在 Git 中对文件改名，可以这么做：

```bash
git mv file_from file_to
```

其实，运行` git mv`就相当于运行了下面三条命令：

```bash
$ mv README.md README
$ git rm README.md
$ git add README
```

如此分开操作，Git 也会意识到这是一次重命名，所以不管何种方式结果都一样。 两者唯一的区别在于，`git mv`是一条命令而非三条命令，直接使用`git mv`方便得多。 不过在使用其他工具重命名文件时，记得在提交前`git rm`删除旧文件名，再` git add`添加新文件名。

## 查看提交历史

在提交了若干更新，又或者克隆了某个项目之后，你也许想回顾下提交历史。 完成这个任务最简单而又有效的工具是` git log`命令。

我们使用一个非常简单的 “simplegit” 项目作为示例。当你在此项目中运行` git log`命令时，可以看到下面的输出：

```bash
$ git logcommit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

		changed the version number
		
commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

		removed unnecessary test
		
commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

		first commit
```

不传入任何参数的默认情况下，`git log`会按时间先后顺序列出所有的提交，最近的更新排在最上面。 正如你所看到的，这个命令会列出每个提交的**SHA-1**校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

其中一个比较有用的选项是` -p`或 `--patch`，它会显示每次提交所引入的差异（按 补丁的格式输出）。 你也可以限制显示的日志条目数量，例如使用` -2`选项来只显示最近的两次提交。

你也可以为` git log`附带一系列的总结性选项。 比如你想看到每次提交的简略统计信息，可以使用` --stat`选项

另一个非常有用的选项是` --pretty`。 这个选项可以使用不同于默认格式的方式展示提交历史。 这个选项有一些内建的子选项供你使用。 比如` oneline`会将每个提交放在一行显示，在浏览大量的提交时非常有用。 另外还有` short`，`full`和` fuller`选项，它们展示信息的格式基本一致，但是详尽程度不一

最有意思的是` format`，可以定制记录的显示格式。 这样的输出对后期提取分析格外有用——因为你知道输出的格式不会随着 Git 的更新而发生改变，`git log --pretty=format`常用的选项列出了` format`接受的常用格式占位符的写法及其代表的意义。

### 限制输出长度

除了定制输出格式的选项之外，`git log`还有许多非常实用的限制输出长度的选项，也就是只输出一部分的提交。 之前你已经看到过` -2`选项了，它只会显示最近的两条提交， 实际上，你可以使用类似` -<n>`的选项，其中的` n`可以是任何整数，表示仅显示最近的` n`条提交。 不过实践中这个选项不是很常用，因为 Git 默认会将所有的输出传送到分页程序中，所以你一次只会看到一页的内容。

但是，类似` --since`和` --until`这种按照时间作限制的选项很有用。 例如，下面的命令会列出最近两周的所有提交：

```bash
git log --since=2.weeks
```

该命令可用的格式十分丰富——可以是类似**"2008-01-15"**的具体的某一天，也可以是类似 **"2 years 1 day3 minutes ago"**的相对日期。

还可以过滤出匹配指定条件的提交。 用` --author`选项显示指定作者的提交，用` --grep`选项搜索提交说明中的关键字。

另一个非常有用的过滤器是` -S`（俗称“**pickaxe**”选项，取“用鹤嘴锄在土里捡石头”之意）， 它接受一个字符串参数，并且只会显示那些添加或删除了该字符串的提交。 

| 选项              | 说明                                       |
| ----------------- | ------------------------------------------ |
| -<n>              | 仅显示最近的 n 条提交。                    |
| --since, --after  | 仅显示指定时间之后的提交。                 |
| --until, --before | 仅显示指定时间之前的提交。                 |
| --author          | 仅显示作者匹配指定字符串的提交。           |
| --committer       | 仅显示提交者匹配指定字符串的提交。         |
| --grep            | 仅显示提交说明中包含指定字符串的提交。     |
| -S                | 仅显示添加或删除内容匹配指定字符串的提交。 |

来看一个实际的例子，如果要在 Git 源码库中查看 Junio Hamano 在 2008 年 10 月其间， 除了合并提交之外的哪一个提交修改了测试文件，可以使用下面的命令：

```bash
git log --pretty="%h - %s" --author='Junio C Hamano' --since="2008-10-01" \  --before="2008-11-01" --no-merges -- t/
```

> 按照你代码仓库的工作流程，记录中可能有为数不少的合并提交，它们所包含的信息通常并不多。 为了避免显示的合并提交弄乱历史记录，可以为` log`加上` --no-merges`选项。

## 撤消操作

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有` --amend`选项的提交命令来重新提交：

```bash
git commit --amend
```

这个命令会将暂存区中的文件提交。 如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令）， 那么快照会保持不变，而你所修改的只是提交信息。

例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：

```bash
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```

最终你只会有一个提交——第二次提交将代替第一次提交的结果。

> 当你在修补最后的提交时，与其说是修复旧提交，倒不如说是完全用一个新的提交替换旧的提交， 理解这一点非常重要。从效果上来说，就像是旧有的提交从未存在过一样，它并不会出现在仓库的历史中。
>
> 修补提交最明显的价值是可以稍微改进你最后的提交，而不会让“啊，忘了添加一个文件”或者 “小修补，修正笔误”这种提交信息弄乱你的仓库历史。

### 取消暂存的文件

你已经修改了两个文件并且想要将它们作为两次独立的修改提交， 但是却意外地输入`git add *`暂存了它们两个。如何只取消暂存两个中的一个呢？ 

使用` git reset HEAD <file>...`来取消暂存。 所以，我们可以这样来取消暂存 `CONTRIBUTING.md`文件：

```bash
$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
M   CONTRIBUTING.md
```

### 撤消对文件的修改

如果你并不想保留对` CONTRIBUTING.md`文件的修改怎么办？ 你该如何方便地撤消修改——将它还原成上次提交时的样子（或者刚克隆完的样子，或者刚把它放入工作目录时的样子）？幸运的是，`git status`也告诉了你应该如何做。

```bash
git checkout -- CONTRIBUTING.md
```

> 请务必记得` git checkout -- <file>`是一个危险的命令。 你对那个文件在本地的任何修改都会消失——Git 会用最近提交的版本覆盖掉它。 除非你确实清楚不想要对那个文件的本地修改了，否则请不要使用这个命令。

## 远程仓库的使用

为了能在任意 Git 项目上协作，你需要知道如何管理自己的远程仓库。 远程仓库是指托管在因特网或其他网络中的你的项目的版本库。 你可以有好几个远程仓库，通常有些仓库对你只读，有些则可以读写。 与他人协作涉及管理远程仓库以及根据需要推送或拉取数据。 管理远程仓库包括了解如何添加远程仓库、移除无效的远程仓库、管理不同的远程分支并定义它们是否被跟踪等等。 

### 查看远程仓库

如果想查看你已经配置的远程仓库服务器，可以运行` git remote`命令。它会列出你指定的每一个远程服务器的简写。 如果你已经克隆了自己的仓库，那么至少应该能看到` origin` ——这是 Git 给你克隆的仓库服务器的默认名字：

```bash
$ git clone https://github.com/schacon/ticgit
Cloning into 'ticgit'...
remote: Reusing existing pack: 1857, done.
remote: Total 1857 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (1857/1857), 374.35 KiB | 268.00 KiB/s, done.
Resolving deltas: 100% (772/772), done.
Checking connectivity... done.
$ cd ticgit
$ git remote
origin
```

你也可以指定选项` -v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的**URL**

```bash
$ git remote -v
origin  https://github.com/schacon/ticgit (fetch)
origin  https://github.com/schacon/ticgit (push)
```

如果你的远程仓库不止一个，该命令会将它们全部列出。

### 添加远程仓库

运行` git remote add <shortname> <url>`添加一个新的远程 Git 仓库，同时指定一个方便使用的简写：

```bash
$ git remote
origin
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin  https://github.com/schacon/ticgit (fetch)
origin  https://github.com/schacon/ticgit (push)
pb  https://github.com/paulboone/ticgit (fetch)
pb  https://github.com/paulboone/ticgit (push)
```

现在你可以在命令行中使用字符串` pb`来代替整个**URL**。 例如，如果你想拉取**Paul** 的仓库中有但你没有的信息，可以运行` git fetch pb`：

```bash
$ git fetch pb
remote: Counting objects: 43, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 43 (delta 10), reused 31 (delta 5)
Unpacking objects: 100% (43/43), done.
From https://github.com/paulboone/ticgit
 * [new branch]      master     -> pb/master
 * [new branch]      ticgit     -> pb/ticgit
```

### 从远程仓库中抓取与拉取

从远程仓库中获得数据，可以执行：

```bash
git fetch <remote>
```

这个命令会访问远程仓库，从中拉取所有你还没有的数据。 执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。

如果你使用` clone`命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 **“origin”** 为简写。 所以，`git fetch origin`会抓取克隆（或上一次抓取）后新推送的所有工作。 必须注意` git fetch`命令只会将数据下载到你的本地仓库——**它并不会自动合并或修改你当前的工作**。 当准备好时你必须手动将其合并入你的工作。

### 推送到远程仓库

当你想分享你的项目时，必须将其推送到上游。 这个命令很简单：`git push <remote> <branch>`。当你想要将` master`分支推送到` origin`服务器时（再次说明，克隆时通常会自动帮你设置好那两个名字）， 那么运行这个命令就可以将你所做的备份到服务器：

```bash
git push origin master
```

只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。 当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 **你必须先抓取他们的工作**并将其合并进你的工作后才能推送。

### 查看某个远程仓库

如果想要查看某一个远程仓库的更多信息，可以使用` git remote show <remote>`命令。如果想以一个特定的缩写名运行这个命令，例如` origin`，会得到像下面类似的信息：

```bash
$ git remote show origin
* remote origin
	Fetch URL: https://github.com/schacon/ticgit
  Push  URL: https://github.com/schacon/ticgit
  HEAD branch: master  Remote branches:
  master                   tracked
  dev-branch               tracked
  Local branch configured for 'git pull':
  master merges with remote master
  Local ref configured for 'git push':
  master pushes to master (up to date)
```

它同样会列出远程仓库的**URL**与跟踪分支的信息。 这些信息非常有用，它告诉你正处于 `master`分支，并且如果运行` git pull`， 就会抓取所有的远程引用，然后将远程 master分支合并到本地` master`分支。 它也会列出拉取到的所有远程引用。

这个命令列出了当你在特定的分支上执行` git push`会自动地推送到哪一个远程分支。 它也同样地列出了哪些远程分支不在你的本地，哪些远程分支已经从服务器上移除了， 还有当你执行` git pull`时哪些本地分支可以与它跟踪的远程分支自动合并。

### 远程仓库的重命名与移除

你可以运行` git remote rename`来修改一个远程仓库的简写名。例如，想要将` pb`重命名为` paul`，可以用`git remote rename`这样做：

```bash
$ git remote rename pb paul
$ git remote
origin
paul
```

值得注意的是这同样也会修改你所有远程跟踪的分支名字。 那些过去引用` pb/master`的现在会引用`paul/master`。

如果因为一些原因想要移除一个远程仓库——你已经从服务器上搬走了或不再想使用某一个特定的镜像了， 又或者某一个贡献者不再贡献了——可以使用` git remote remove`或` git remote rm`：

```bash
$ git remote remove paul
$ git remote
origin
```

一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起被删除。

## 打标签

像其他版本控制系统（VCS）一样，Git 可以给仓库历史中的某一个提交打上标签，以示重要。 比较有代表性的是人们会使用这个功能来标记发布结点（v1.0、 v2.0等等）。 

### 列出标签

在 Git 中列出已有的标签非常简单，只需要输入` git tag`（可带上可选的` -l`选项` --list`）：

```bash
$ git tag
v1.0
v2.0
```

你也可以按照特定的模式查找标签。 例如，Git 自身的源代码仓库包含标签的数量超过 500 个。 如果只对 1.8.5系列感兴趣，可以运行：

```bash
git tag -l "v1.8.5*"
```

### 创建标签

Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）。

轻量标签很像一个不会改变的分支——它只是某个特定提交的引用。

而附注标签是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。 通常会建议创建附注标签，这样你可以拥有以上所有信息。但是如果你只是想用一个临时的标签， 或者因为某些原因不想要保存这些信息，那么也可以用轻量标签。

### 附注标签

在 Git 中创建附注标签十分简单。 最简单的方式是当你在运行` tag`命令时指定` -a`选项：

```bash
$ git tag -a v1.4 -m "my version 1.4"
$ git tag
v0.1
v1.3
v1.4
```

`-m`选项指定了一条将会存储在标签中的信息。 如果没有为附注标签指定一条信息，Git 会启动编辑器要求你输入信息。

通过使用` git show`命令可以看到标签信息和与之对应的提交信息：

```bash
$ git show v1.4
tag v1.4
Tagger: Ben Straub <ben@straub.cc>
Date:   Sat May 3 20:19:12 2014 -0700

my version 1.4

commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

changed the version number
```

输出显示了打标签者的信息、打标签的日期时间、附注信息，然后显示具体的提交信息。

### 轻量标签

另一种给提交打标签的方式是使用轻量标签。 轻量标签本质上是将提交校验和存储到一个文件中——没有保存任何其他信息。 创建轻量标签，不需要使用` -a`、`-s`或 `-m`选项，只需要提供标签名字：

```bash
$ git tag v1.4-lw
$ git tag
v0.1
v1.3
v1.4
v1.4-lw
v1.5
```

这时，如果在标签上运行` git show`，你不会看到额外的标签信息。命令只会显示出提交信息：

```bash
$ git show v1.4-lw
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

changed the version number
```

---

> 本文源于《Pro Git 第二版》第二章
>
> https://git-scm.com/book/zh/v2
>
> 在线学习git
>
> https://learngitbranching.js.org/?locale=zh_CN
