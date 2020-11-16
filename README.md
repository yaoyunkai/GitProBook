Git Pro Book
=====

## 1. Start ##

### 1.3 What is Git ###

**Git 保证完整性**

Git 中所有的数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件内容或目录内容。 这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部分。

**三种状态**

已提交 committed 已修改 modified 已暂存 staged

- 已修改表示修改了文件，但还没保存到数据库中。
- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。
- 已提交表示数据已经安全地保存在本地数据库中。

Git 项目拥有三个阶段：工作区，暂存区 和 Git目录

![工作区、暂存区以及 Git 目录。](README.assets/areas.png)

工作区是对项目的某个版本独立提取出来的内容。 这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。

暂存区是一个文件，保存了下次将要提交的文件列表信息，一般在 Git 仓库目录中。 按照 Git 的术语叫做“索引”，不过一般说法还是叫“暂存区”。

Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。 这是 Git 中最重要的部分，从其它计算机克隆仓库时，复制的就是这里的数据。

基本的 Git 工作流程如下：

1. 在工作区中修改文件。
2. 将你想要下次提交的更改选择性地暂存，这样只会将更改的部分添加到暂存区。
3. 提交更新，找到暂存区的文件，将快照永久性存储到 Git 目录。

如果 Git 目录中保存着特定版本的文件，就属于 **已提交** 状态。 如果文件已修改并放入暂存区，就属于 **已暂存** 状态。 如果自上次检出后，作了修改但还没有放到暂存区域，就是 **已修改** 状态。

### 1.6 Git 配置方式 ###

1. 系统级别的配置: `/etc/gitconfig` git 选项是 --system
2. 用户级别的配置: `~/.gitconfig` 只对当前用户有效 你可以传递 `--global` 选项让 Git 读写此文件，这会对你系统上 **所有** 的仓库生效。
3. 当前仓库级别的配置 `.git/config` 只对当前仓库有效。

你可以通过以下命令查看所有的配置以及它们所在的文件：

```console
$ git config --list --show-origin
```

**用户信息配置**

```console
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

如果使用了 `--global` 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。 当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 `--global` 选项的命令来配置。

**配置文本编辑器**

```console
$ git config --global core.editor emacs
```

**显示所有配置信息**

```console
C:\Users\Liberty>git config -l
diff.astextplain.textconv=astextplain
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.process=git-lfs filter-process
filter.lfs.required=true
http.sslbackend=openssl
http.sslcainfo=d:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
core.autocrlf=false
core.fscache=true
core.symlinks=false
pull.rebase=false
credential.helper=manager
```

你可以通过输入 `git config <key>`： 来检查 Git 的某一项配置

```console
$ git config user.name
John Doe
```

此时，你可以查询 Git 中该变量的 **原始** 值，它会告诉你哪一个配置文件最后设置了该值：

```console
$ git config --show-origin rerere.autoUpdate
file:/home/johndoe/.gitconfig	false
```

## 2. Git Base ##

### 2.1 获取Git仓库 ###

通常有两种获取 Git 项目仓库的方式：

1. 将尚未进行版本控制的本地目录转换为 Git 仓库；
2. 从其它服务器克隆 一个已存在的 Git 仓库。

**clone 现有的仓库**

如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以通过额外的参数指定新的目录名：

```console
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

### 2.2 记录更新 ###

请记住，你工作目录下的每一个文件都不外乎这两种状态：**已跟踪** 或 **未跟踪** 。

![Git 下文件生命周期图。](README.assets/lifecycle.png)

**检查当前文件状态**

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

如果之前并不存在这个文件，使用 git status 命令，你将看到一个新的未跟踪文件：

```console
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
```

只要在 `Changes to be committed` 这行下面的，就说明是已暂存状态。

```console
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   demo03.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   demo_01.txt
```

 出现在 `Changes not staged for commit` 这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。将这个命令理解为“精确地将内容添加到下一次提交中”

**状态简览**

```console
$ git status -s
A  demo03.txt
M  demo_01.txt
```

**忽略文件**

在这种情况下，我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式

**查看已暂存和未暂存的修改**

`git diff` 命令比较的是工作目录中当前文件和暂存区域快照之间的差异。 也就是修改之后还没有暂存起来的变化内容。

```console
$ git diff
diff --git a/demo03.txt b/demo03.txt
index e69de29..6d34f32 100644
--- a/demo03.txt
+++ b/demo03.txt
@@ -0,0 +1,2 @@
+
+xxxxxxxxxxxxxxxxxxxxx
```

若要查看已暂存的将要添加到下次提交里的内容，可以用 `git diff --staged` 命令。 这条命令将比对已暂存文件与最后一次提交的文件差异：

```console
$ git diff --staged
diff --git a/demo_01.txt b/demo_01.txt
index b5b5cd2..1acd41d 100644
--- a/demo_01.txt
+++ b/demo_01.txt
@@ -1,3 +1,5 @@
 write a new line

 xxxxxxxxxxxxxxxxxxxxxxx
+
+yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy
```

```console
Liberty@LAPTOP-P85CKKBA MINGW64 ~/Downloads/packages/git-demo-package (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   demo03.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   demo03.txt


Liberty@LAPTOP-P85CKKBA MINGW64 ~/Downloads/packages/git-demo-package (master)
$ git diff
diff --git a/demo03.txt b/demo03.txt
index 0f67f12..5bdfd42 100644
--- a/demo03.txt
+++ b/demo03.txt
@@ -2,3 +2,6 @@
 xxxxxxxxxxxxxxxxxxxxx

 vvvvvvvvvvvvvvvvvvvvvvvvv
+
+
+ttttttttttttttttttttttttttttt

Liberty@LAPTOP-P85CKKBA MINGW64 ~/Downloads/packages/git-demo-package (master)
$ git diff --cached
diff --git a/demo03.txt b/demo03.txt
index 6d34f32..0f67f12 100644
--- a/demo03.txt
+++ b/demo03.txt
@@ -1,2 +1,4 @@

 xxxxxxxxxxxxxxxxxxxxx
+
+vvvvvvvvvvvvvvvvvvvvvvvvv

```

**提交更新**

请记住，提交时记录的是放在暂存区域的快照。 任何还未暂存文件的仍然保持已修改状态，可以在下次提交时纳入版本管理。

**移除文件**

要从Git中移除文件，必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。 可以用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件。

如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项 `-f`

我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。`git rm --cached README`

**移动文件**

### 2.3 查看提交历史 ###

**查看提交历史**

`git log`

1, 显示每次提交所引入的差异（按 **补丁** 的格式输出）`git log -p -2`  `--patch ` `-2 `： 表示最近多少次的提交记录

```console
$ git log --patch -2
commit ca82a6dff817ec66f44342007202690a93763949 (HEAD -> master, origin/master, origin/HEAD)
Author: Scott Chacon <schacon@gmail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the verison number

diff --git a/Rakefile b/Rakefile
index a874b73..8f94139 100644
--- a/Rakefile
+++ b/Rakefile
@@ -5,7 +5,7 @@ require 'rake/gempackagetask'
 spec = Gem::Specification.new do |s|
     s.platform  =   Gem::Platform::RUBY
     s.name      =   "simplegit"
-    s.version   =   "0.1.0"
+    s.version   =   "0.1.1"
     s.author    =   "Scott Chacon"
     s.email     =   "schacon@gmail.com"
     s.summary   =   "A simple gem for using Git in Ruby code."

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gmail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test code

diff --git a/lib/simplegit.rb b/lib/simplegit.rb
index a0a60ae..47c6340 100644
--- a/lib/simplegit.rb
+++ b/lib/simplegit.rb
@@ -18,8 +18,3 @@ class SimpleGit
     end

 end
-
-if $0 == __FILE__
-  git = SimpleGit.new
-  puts git.show
-end
```

2, 查看每次提交的简略统计信息, `--stat`

```console
$ git log --stat
commit ca82a6dff817ec66f44342007202690a93763949 (HEAD -> master, origin/master, origin/HEAD)
Author: Scott Chacon <schacon@gmail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the verison number

 Rakefile | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gmail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test code

 lib/simplegit.rb | 5 -----
 1 file changed, 5 deletions(-)

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gmail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

    first commit

 README           |  6 ++++++
 Rakefile         | 23 +++++++++++++++++++++++
 lib/simplegit.rb | 25 +++++++++++++++++++++++++
 3 files changed, 54 insertions(+)
```

3, 提供不同于默认格式的显示方式: `--pretty`

```console
$ git log --pretty=oneline
ca82a6dff817ec66f44342007202690a93763949 (HEAD -> master, origin/master, origin/HEAD) changed the verison number
085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7 removed unnecessary test code
a11bef06a3f659402fe7563abf99ad00de2209e6 first commit
```

4, 定制记录的显示格式： `--pretty=format`

```console
$ git log --pretty=format:"%h - %an, %ar : %s"
ca82a6d - Scott Chacon, 13 years ago : changed the verison number
085bb3b - Scott Chacon, 13 years ago : removed unnecessary test code
a11bef0 - Scott Chacon, 13 years ago : first commit
```

![image-20201113234439517](README.assets/image-20201113234439517.png)

作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。

4, `--graph` 展示分支，合并历史

```console
$ git log --pretty=format:"%h %s" --graph
* ca82a6d changed the verison number
* 085bb3b removed unnecessary test code
* a11bef0 first commit
```

**GIT LOG 的常用选项**

![image-20201113234754426](README.assets/image-20201113234754426.png)

**限制输出长度**

1， `--since` `--until` 按照时间限制。

2， `--author` 指定提交的作者

3，`--grep` 搜索提交说明中的关键字

4，`-S` 显示添加或者删除该字符串代码的提交。

5，`--no-merges` 不显示合并的提交历史

![image-20201114000038841](README.assets/image-20201114000038841.png)

### 2.4 撤销更改 ###

1，使用 `--amend` 重新提交

`git -commit --amend` 此命令会将暂存区的文件提交。自上次提交以来你还未做任何修改， 那么快照会保持不变，而你所修改的只是提交信息。

```console
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```

最终你只会有一个提交——第二次提交将代替第一次提交的结果。

**取消暂存的文件**

`git reset HEAD demo01.txt`

```console
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   demo01.txt
        modified:   demo03.txt

Liberty@LAPTOP-P85CKKBA MINGW64 ~/Downloads/packages/git-demo-package (master)
$ git reset HEAD demo01.txt
Unstaged changes after reset:
M       demo01.txt

Liberty@LAPTOP-P85CKKBA MINGW64 ~/Downloads/packages/git-demo-package (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   demo03.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   demo01.txt
```

**撤销对文件的修改**

撤销对未提交到暂存区更改的文件：`git checkout  -- <filename>`

### 2.5 远程仓库的使用 ###

**1. 查看远程仓库**

`git remote`

`-v` 选项显示 URL：

```console
$ git remote -v
origin  https://github.com/schacon/ticgit (fetch)
origin  https://github.com/schacon/ticgit (push)
```

**2. 添加远程仓库**

`git remote add  <shortname> <url>`

```console
$ git remote
origin
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
pb	https://github.com/paulboone/ticgit (fetch)
pb	https://github.com/paulboone/ticgit (push)
```

拉取指定仓库的信息： `git fetch <shortname>`  执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。

必须注意 `git fetch` 命令只会将数据下载到你的本地仓库,它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。

你的当前分支设置了跟踪远程分支（阅读下一节和 [Git 分支](https://git-scm.com/book/zh/v2/ch00/ch03-git-branching) 了解更多信息）， 那么可以用 `git pull` 命令来自动抓取后合并该远程分支到当前分支。 

默认情况 git clone 命令会自动设置本地 master 分支跟踪克隆的远程的master分支。运行 `git pull` 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。

**推送到远程仓库**

`git push <remote> <branch>`

**查看远程仓库**

```console
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/schacon/ticgit
  Push  URL: https://github.com/schacon/ticgit
  HEAD branch: master
  Remote branches:
    master tracked
    ticgit tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
```

**远程仓库的重命名与移除**

`git remote rename`

`git remote remove <shortname>`

### 2.6 标签 ###

**1. 列出标签**

`git tag [--list]`

```console
$ git tag -l "v1.8.5*"
v1.8.5
v1.8.5-rc0
v1.8.5-rc1
v1.8.5-rc2
v1.8.5-rc3
v1.8.5.1
v1.8.5.2
v1.8.5.3
v1.8.5.4
v1.8.5.5
```

**2. 创建标签**

Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）。

- 附注标签: `git tag -a v1.4 -m "my version 1.4"`

- 轻量标签：`git tag v1.4-w`

- 后期打标签: `git tag -a v1.2 -m "message" sha1`

**3. 共享标签**

`git push origin <tagname>`

`git push origin --tags`

**4. 删除标签**

删除本地标签： `git tag -d <tagname>`

上述命令并不会从任何远程仓库移除标签: `git push <remote> :refs/tags/<tagname>`

第二种删除远程标签的方式： `git push origin --delelte <tagname>`

**5. 检出标签**

### 2.7 Git 别名 ###

## 3. 分支 ##

### 3.1 分支简介 ###

首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象， 而由多个分支合并产生的提交对象有多个父对象.

Git 会先计算每一个子目录（本例中只有项目根目录）的校验和， 然后在 Git 仓库中将这些校验和保存为树对象。随后，Git 便会创建一个提交对象， 它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。

第一次提交时：

![首次提交对象及其树结构。](README.assets/commit-and-tree.png)

以后的提交：

![提交对象及其父对象。](README.assets/commits-and-parents.png)

Git的分支在本质上仅是提交对象的可变指针。`master` 分支会在每次提交时自动向前移动。

![分支及其提交历史。](README.assets/branch-and-history.png)

**1，创建分支**

`git branch <branch_name>` , 创建分支后会在提交对象上创建一个指针。

![两个指向相同提交历史的分支。](README.assets/two-branches.png)

git 通过 `HEAD` 指针指向当前的所在的本地分支。

![HEAD 指向当前所在的分支。](README.assets/head-to-master.png)

使用 `git log --oneline --decorate` 查看各个分支当前所指的对象。

```console
$ git log --oneline --decorate
70fb9cc (HEAD -> main, origin/main, dev_01) git branch
9b4f5d7 update README
5dbfa77 (tag: v1.0) update docs
cef1a00 git pro book
```

**2, 分支切换**

`git checkout  dev_01`

![HEAD 指向当前所在的分支。](README.assets/head-to-testing.png)

```console
$ git log --oneline --decorate
80e5820 (HEAD -> dev_01) checkout branch
70fb9cc (origin/main, main) git branch
9b4f5d7 update README
5dbfa77 (tag: v1.0) update docs
cef1a00 git pro book
```

查看提交的分叉历史:

```console
$ git log --oneline --decorate --graph --all
* dc6f37d (dev_02) crearte hello world 02
| * 7250c71 (HEAD -> main) add hello world txt
|/
* bf7bb33 (origin/main) update README
* c9b8723 (dev_01) update dev_01
* 80e5820 checkout branch
* 70fb9cc git branch
* 9b4f5d7 update README
* 5dbfa77 (tag: v1.0) update docs
* cef1a00 git pro book
```

创建新的分支并切换到新的分支： `git checkout -b <new_branch>`

### 3.2 分支的新建与合并 ###

对于 “fast-forward” 的解释，由于我想要合并的分支的提交 `C4` 是我所在分支最新提交`C2` 的直接后继，因此Git会直接将指针向前移动。当你试图合并两个分支时， 如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候， 只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧。

**1，分支合并**

![一次典型合并中所用到的三个快照。](README.assets/basic-merging-1.png)

在这种情况下，你的开发历史从一个更早的地方开始分叉开来（diverged）。 因为，`master` 分支所在提交并不是 `iss53` 分支所在提交的直接祖先，Git 不得不做一些额外的工作。 出现这种情况的时候，Git 会使用两个分支的末端所指的快照（`C4` 和 `C5`）以及这两个分支的公共祖先（`C2`），做一个简单的三方合并。

Git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它。 这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。这里这次提交父指针指向了两个提交。

![一个合并提交。](README.assets/basic-merging-2.png)

**2，遇到冲突时的分支合并**

如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们。

### 3.3 分支管理 ###

**1， 查看每个分支的最后一次提交**

```console
$ git branch -v
  devlop 50cac7b [gone] conflict test
* main   4810460 merge all
```

**2, 查看合并到当前分支的分支，或者没有**

`git branch --merged --no-merged`

### 3.5 远程分支 ###

**1，获取远程引用的完整列表**

```console
$ git ls-remote origin
48104600a5639eb25c5008a3ecd49b9f7910de9a        HEAD
48104600a5639eb25c5008a3ecd49b9f7910de9a        refs/heads/main
```

使用 `git remote show <remote>` 获取远程分支的信息。

远程跟踪分支是远程分支状态的引用。

当第一次从远程克隆仓库时：

![克隆之后的服务器与本地仓库。](README.assets/remote-branches-1.png)

**2， 推送**

```console
$ git push origin serverfix
Counting objects: 24, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (24/24), 1.91 KiB | 0 bytes/s, done.
Total 24 (delta 2), reused 0 (delta 0)
To https://github.com/schacon/simplegit
 * [new branch]      serverfix -> serverfix
```

**3，跟踪分支**

```console
$ git checkout --track origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

如果你尝试检出的分支 (a) 不存在且 (b) 刚好只有一个名字与之匹配的远程分支，那么 Git 就会为你创建一个跟踪分支：

```console
$ git checkout serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

将本地分支与远程分支设置为不同的名字：

```console
$ git checkout -b sf origin/serverfix
Branch sf set up to track remote branch serverfix from origin.
Switched to a new branch 'sf'
```

设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支， 你可以在任意时间使用 `-u` 或 `--set-upstream-to` 选项运行 `git branch` 来显式地设置。

```console
$ git branch -u origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
```

查看设置的所有跟踪分支：

```console
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
  
$ git fetch --all 抓取所有远程仓库
```

**4，拉取**

区分 `git pull` 与 `git fetch` 的区别。

**5，删除远程分支**

```console
$ git push origin --delete serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```

### 3.6 变基 ###

**1, 变基的基本操作**

![分叉的提交历史。](README.assets/basic-rebase-1.png)

首先找到这两个分支（即当前分支 `experiment`、变基操作的目标基底分支 `master`） 的最近共同祖先 `C2`，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件， 然后将当前分支指向目标基底 `C3`, 最后以此将之前另存为临时文件的修改依序应用。 （译注：写明了 commit id，以便理解，下同）

![将 `C4` 中的修改变基到 `C3` 上。](README.assets/basic-rebase-3.png)

现在回到 `master` 分支，进行一次快进合并。

```console
$ git checkout master
$ git merge experiment
```

![`master` 分支的快进合并。](README.assets/basic-rebase-4.png)

变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。

## 7. Git Tool ##

### 7.1 选择修订版本 ###

**1, 缩短 SHA-1**

```console
$ git log --abbrev-commit --pretty=oneline
ca82a6d changed the version number
085bb3b removed unnecessary test code
a11bef0 first commit
```

**2, 分支引用**

引用特定提交的一种直接方法是，若它是一个分支的顶端的提交， 那么可以在任何需要引用该提交的 Git 命令中直接使用该分支的名称。

```console
$ git show ca82a6dff817ec66f44342007202690a93763949
$ git show topic1
```

**3，引用日志**

当你在工作时， Git 会在后台保存一个引用日志（reflog）， 引用日志记录了最近几个月你的 HEAD 和分支引用所指向的历史。

```console
$ git reflog -5
5819638 (HEAD -> main, origin/main) HEAD@{0}: commit: finish command of f 'rebase'
9a95f3b HEAD@{1}: commit: remove rebase test
4828ad7 HEAD@{2}: merge server: Fast-forward
e9abe77 HEAD@{3}: checkout: moving from server to main
4828ad7 HEAD@{4}: rebase (finish): returning to refs/heads/server
```

**4, 提交区间**

找出在一个分支而不再另一个分支中的提交, 比如：想要查看 experiment 分支中还有哪些提交尚未被合并入 master 分支。在后者中而不在前者中的提交内容：

`git log master..experiment`

`git log origin/master..HEAD`

### 7.2 交互式暂存 ###

运行 `git add` 时使用 `-i` 或者 `--interactive` 选项，Git 将会进入一个交互式终端模式，显示类似下面的东西：

```console
$ git add -i
           staged     unstaged path
  1:    unchanged        +0/-1 TODO
  2:    unchanged        +1/-1 index.html
  3:    unchanged        +5/-1 lib/simplegit.rb

*** Commands ***
  1: [s]tatus     2: [u]pdate      3: [r]evert     4: [a]dd untracked
  5: [p]atch      6: [d]iff        7: [q]uit       8: [h]elp
What now>
```

### 7.3 贮藏与清理 ###

贮藏（stash）会处理工作目录的脏的状态——即跟踪文件的修改与暂存的改动——然后将未完成的修改保存到一个栈上， 而你可以在任何时候重新应用这些改动（甚至在不同的分支上）。

此时，你可以切换分支并在其他地方工作；你的修改被存储在栈上。 要查看贮藏的东西，可以使用 `git stash list`：

```console
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
```

### 7.5 搜索 ###

默认情况下 `git grep` 会查找你工作目录的文件。 第一种变体是，你可以传递 `-n` 或 `--line-number` 选项数来输出 Git 找到的匹配行的行号。

```console
$ git grep -n gmtime_r
compat/gmtime.c:3:#undef gmtime_r
compat/gmtime.c:8:      return git_gmtime_r(timep, &result);
compat/gmtime.c:11:struct tm *git_gmtime_r(const time_t *timep, struct tm *result)
compat/gmtime.c:16:     ret = gmtime_r(timep, result);
compat/mingw.c:826:struct tm *gmtime_r(const time_t *timep, struct tm *result)
compat/mingw.h:206:struct tm *gmtime_r(const time_t *timep, struct tm *result);
date.c:482:             if (gmtime_r(&now, &now_tm))
date.c:545:             if (gmtime_r(&time, tm)) {
date.c:758:             /* gmtime_r() in match_digit() may have clobbered it */
git-compat-util.h:1138:struct tm *git_gmtime_r(const time_t *, struct tm *);
git-compat-util.h:1140:#define gmtime_r git_gmtime_r
```

**Git 日志搜索**

```console
$ git log -S ZLIB_BUF_MAX --oneline
e01503b zlib: allow feeding more than 4GB in one go
ef49a7a zlib: zlib can only process 4GB at a time
```

例如，假设我们想查看 `zlib.c` 文件中`git_deflate_bound` 函数的每一次变更， 我们可以执行 `git log -L :git_deflate_bound:zlib.c`。 Git 会尝试找出这个函数的范围，然后查找历史记录，并且显示从函数创建之后一系列变更对应的补丁。

```console
$ git log -L :git_deflate_bound:zlib.c
commit ef49a7a0126d64359c974b4b3b71d7ad42ee3bca
Author: Junio C Hamano <gitster@pobox.com>
Date:   Fri Jun 10 11:52:15 2011 -0700

    zlib: zlib can only process 4GB at a time

diff --git a/zlib.c b/zlib.c
--- a/zlib.c
+++ b/zlib.c
@@ -85,5 +130,5 @@
-unsigned long git_deflate_bound(z_streamp strm, unsigned long size)
+unsigned long git_deflate_bound(git_zstream *strm, unsigned long size)
 {
-       return deflateBound(strm, size);
+       return deflateBound(&strm->z, size);
 }


commit 225a6f1068f71723a910e8565db4e252b3ca21fa
Author: Junio C Hamano <gitster@pobox.com>
Date:   Fri Jun 10 11:18:17 2011 -0700

    zlib: wrap deflateBound() too

diff --git a/zlib.c b/zlib.c
--- a/zlib.c
+++ b/zlib.c
@@ -81,0 +85,5 @@
+unsigned long git_deflate_bound(z_streamp strm, unsigned long size)
+{
+       return deflateBound(strm, size);
+}
+
```

### 7.6 重写历史 ###

**1，修改最后一次提交**

`git commit --amend` or `git commit --amend --no-edit`

2, 使用 reset revert checkout

**2, git reset**

`git reset` 有三种模式：soft mixed hard

![img](README.assets/4428238-fcad08ebe26933a6.png)

- **HARD** : 会重置HEAD指针，并且会重置 index 和 工作目录里的内容，stage区和工作目录里的内容会被完全重置为和**HEAD**的新位置相同的内容。
- 

