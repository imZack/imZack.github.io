title: 修改 Git commits 的作者資訊
date: 2014-11-29 15:14:29
tags:
  - git
---

昨天晚上在家改公司的專案，一時不察就直接 `git commit -am "回家寫扣真辛苦"`，赫然發現...啊啊啊啊 **Git 作者** 是我平常在外面闖蕩用的 name + email

這下該怎辦呢？好在找到這篇 [StackOverflow](http://stackoverflow.com/questions/3042437/change-commit-author-at-one-specific-commit) 上的文章，一步一步教你改...

<!-- more -->

假設今天 git history 有三筆 commits A->B->C->D->E->F，F 是 HEAD 也就是目前最新的。

假如我想要更改 DEF 這三個在家裡做的 commit 只需要下列步驟：

1. 目錄下指令 `git rebase -i C`
2. 會出現 `pick D pick E pick F` 把 pick 改為 **edit**，關閉後就會進入 rebase 流程
3. 此時你會停在 D 這個 commit 上頭，輸入 `git commit --amend --author="Author Name <email@address.com>"`
4. `git rebase --continue`
5. 就會移動到 E，以此類推重複**步驟 3, 4**
6. 最後都完成就大功告成啦！

**PS1.** 如果是像我把 Repo 放在 Dropbox 內然後在不同電腦寫 code 的話，可以輸入下面指令
```
git config user.name "YOUR NAME"
git config user.email "YOUR EMAIL"
```
如此一來，就不會去用到 git global config 了

**PS2.** 除此之外 Github 也有提供大量更改過去 commit 的方法 (git filter-branch) [Script 在此](https://help.github.com/articles/changing-author-info/)

