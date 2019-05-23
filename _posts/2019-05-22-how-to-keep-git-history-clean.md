---
layout: post
title: "保持Git的历史记录清晰"
description: ""
category: 
tags: []
---
{% include JB/setup %}
### Git的历史记录可能长这样

<div style="text-align:center"><img src ="/assets/images/1-nonlinear-vs-linear.png" style="height:300px;" /></div>

##### 需要知道
> Git merge是将一个分支上的commit合并，生成一个合并的commit，然后应用到目标分支上，像这样：
 
	  A---B---C topic
	 /
    D---E---F---G master
    
    master分支上git merge topic后变成：
    
      A---B---C topic
	 /         \
    D---E---F---G---H master
    
> Git Rebase是将一个分支上的commit复制一份（不同的commit id，相同的内容），放到目标分支上，像这样：

          A---B---C topic
         /
    D---E---F---G master
    
    topic分支上git rebase master或 git rebase master topic后变成：
    
                  A'--B'--C' topic
                 /
    D---E---F---G master
    
    note：有A commit的branch在rebase另一个有A‘ commit的branch时，git会把两个commit识别为同一个。
    
##### 根据项目需求取舍
> 两个branch合并时，
> * 如需看到branch间的关系，选择<code>git merge source-branch</code>
> * 如果branch太多，git log线路错综复杂并无参考意义，选择<code>git rebase destination-branch</code>

同理，
> git pull时，实际为remote branch和local branch的合并
> * 如只用git pull, log的history会很多分支错综复杂，类似每次更新代码都做一次git merge，主线上branch横飞如开头的no-one-line-branches一样。

> * 如用git pull --rebase, log的history会保持一条主线（trunk base），同理每次更新代码都做一次git rebase master origin/master，所有人的commit都按顺序放在那条主线上，如开头的one-line-branches一样。


##### Reference
* <a href="https://git-scm.com/docs/git-rebase">Rebase Doc</a>

* <a href="https://git-scm.com/docs/git-merge">Merge Doc</a>


