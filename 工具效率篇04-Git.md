---
title: 工具效率篇04-Git
date: 2020-06-17 22:56:28
categories:
	- 常用工具 
tags:
	- Git
---

### Git数据模型

在git中，一个文件为一个`bolb`，就是一些bytes。一个目录称为一个`tree`，将`names`映射为`blobs or trees`。

一个`snapshot`是并tracked的top-level tree。如下是一个示例。`top-level tree`包含两个元素，一个tree`foo`(foo包含一个元素，a blob "bar.txt"),和一个blob`baz.txt`

```txt
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

下面是数据模型的伪代码

```c
// a file is a bunch of bytes
type blob = array<bote>

// a directory contains named files and directories
type tree = map<string, tree | blob>

// a commit has parents, metadata, and the top-level tree
type commit = struct{
  parent: array<commit>   // 每个commit可能有多个parent
  author: string 
  message: string
  snapshot: tree     // 这个commit对应的snapshot
}
```

`object`是一个blob、tree或者commit

```c
type object = blob | tree | commit
```

在Git的数据存储中，所有的`objects`都是通过它的内容生成的`SHA1 has`来进行定位的。(`content-addressed`)

`object`都是`immutable`的。

```python
# 所有的object存储到map中，key是其sha1值
objects = map<string, object>
 
def store(object):
  id = sha1(object)
  objects[id] = object

def load(id):
  return objects[id]
```

**References**

所有的`object`可以通过`SHA1`来标记，但是一共是40为的16进制字符，不是很方便。为`SHA1`起一些人类易读的名字，称作`references`。`references`是指向`commits`的指针，是可以修改的。(可以让它指向一个新的commit)。

`master`reference通常指向主分支的最后一次commit。

```python
references = map<string, string>  # 一个reference对应一个commit的SHA1
def update_reference(name, id):
  references[name] = id
  
def read_reference(name):
  return references[name]

def load_reference(name_or_id):
  if name_or_id in references:
    return load(references[name_or_id])
  else:
    return load(name_or_id)
```

有了`references`，Git就可以使用例如`master`这种来指向一个特定的`snapshot`。

在Git中,`HEAD`是一个特殊的`reference`，指向了where we currently are。

一般是`HEAD -> master -> C1`的指向关系，可以通过`checkout`让`HEAD`直接指向`commit`。

**Modeling history: relating snapshots**

在Git中，历史是一个由`snapshots`组成的有向无环图(DAG)。在图中每个节点有一组父节点，而不是线性的，这样可以支持将多分支`merge`的操作。

Git将`snapshots`称作`commits`。下面是一个示意图，每个圆圈是一个`commit(snapshot)`

```txt
o <-- o <-- o <-- o
            ^  
             \
              --- o <-- o
              
o <-- o <-- o <-- o <---- o
            ^            /
             \          v
              --- o <-- o    # 两个branch合并的结果
```

Commits in Git are immutable. This doesn’t mean that mistakes can’t be corrected, however; it’s just that “edits” to the commit history are actually creating entirely new commits, and references (see below) are updated to point to the new ones.

**Repositories**

一个Git`repository` = data`objects` + `references`

Git存储的就是objects和references，这就是所有的Git的数据模型。所有的git命令都会转换为对commit DAG的操作。如增加objets，增加或者更新references。

Whenever you’re typing in any command, think about what manipulation the command is making to the underlying graph data structure.

### Staging area

如果命令直接基于现在的文件状态来创建快照的话，那么有可能有些修改我们不希望加入到快照中，比如一些日志文件等。或者实现了两个feature，但是想分别为两个各弄一个commit。Git通过引入`Staging area`的机制来可以自己决定下一个快照加入哪些修改。

<img src="工具效率篇04-Git/01.png"  />

`git add`加入到`staging area`

`git commit` 创建一个`snapshot`

`git checkout`将快照的内容恢复到工作区，如果这个时候工作区有为未提交的修改，可能会丢失。为了避免丢失，可以先`stash`起来。

### Git 常用命令

#### Basic

- `git help <command>`: get help for a git command
- `git init`: creates a new git repo, with data stored in the `.git` directory
- `git status`: tells you what’s going on
- `git add <filename>`: adds files to staging area
- `git commit`: creates a new commit
  - Write [good commit messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
  - Even more reasons to write [good commit messages](https://chris.beams.io/posts/git-commit/)!
- `git log`: shows a flattened log of history
- `git log --all --graph --decorate`: visualizes history as a DAG
- `git diff <filename>`: show differences since the last commit
- `git diff <revision> <filename>`: shows differences in a file between snapshots
- `git checkout <revision>`: updates HEAD and current branch

- `git log --all --graph --decorate`：**图形化展示commit的DAG**

#### Branching ans merging

- `git branch`: shows branches
- `git branch <name>`: creates a branch
- `git checkout -b <name>`: creates a branch and switches to it
  - same as `git branch <name>; git checkout <name>`
- `git merge <revision>`: merges into current branch
- `git mergetool`: use a fancy tool to help resolve merge conflicts
- `git rebase`: rebase set of patches onto a new base

#### Remotes

- `git remote`: list remotes
- `git remote add <name> <url>`: add a remote
- `git push <remote> <local branch>:<remote branch>`: send objects to remote, and update remote reference
- `git branch --set-upstream-to=<remote>/<remote branch>`: set up correspondence between local and remote branch
- `git fetch`: retrieve objects/references from a remote
- `git pull`: same as `git fetch; git merge`
- `git clone`: download repository from remote

#### Undo

- `git commit --amend`: edit a commit’s contents/message
- `git reset HEAD <file>`: unstage a file
- `git checkout -- <file>`: discard changes

#### Advanced Git

- `git config`: Git is [highly customizable](https://git-scm.com/docs/git-config)
- `git clone --depth=1`: shallow clone, without entire version history
- `git add -p`: interactive staging
- `git rebase -i`: interactive rebasing
- `git blame`: show who last edited which line
- `git stash`: temporarily remove modifications to working directory
- `git bisect`: binary search history (e.g. for regressions)
- `.gitignore`: [specify](https://git-scm.com/docs/gitignore) intentionally untracked files to ignore

