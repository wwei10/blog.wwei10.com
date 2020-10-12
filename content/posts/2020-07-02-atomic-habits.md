---
title: "原子习惯"
date: 2020-07-02 15:00:00
slug: atomic-habits
---

最近读了原子习惯（atomic habits），受了不少启发。尤其是WFH期间，习惯的重要性变得越发重要，想和大家分享一下我的得到。


<!--more-->

## 习惯的四要素

![Atomic Habits](/images/atomic-habits.png)

下面举我自己的例子具体说说这四个要素。

提示：一看到我的PS4，就会激发我玩游戏的渴望。

渴望：渴望玩游戏获得胜利的成就感。

回应：拿起手柄，打开电视，开始玩。

奖赏：获胜，通关，得到满足。

理解了这四个要素就可以更有针对性的改变自己的习惯。

## 改变习惯的四个法则

![Atomic Habits](/images/atomic-habits-laws.png)

想改变习惯，就要抓住习惯四要素中的一点或几点来做调整。

### 让提示显而易见

> Make it obvious / make it invisible

环境是人类行为中极为重要的一环。要想养成一个好习惯或纠正一个坏习惯，改变环境，让有益的提示更明显，让有害的提示看不见。还是拿玩PS4举例子。我的一项举措就是把PS4主机还有手柄都藏到一个平常我看不到的地方，眼不见，心为净。没有了提示，自然也就没了之后的渴望和行动。

### 让习惯有吸引力

> Make it attrative / make it unattractive

游戏之所以让人着迷，是因为它提供的成就感，幸福感。玩游戏是非常容易进入心流状态的一项活动。之前有一篇博客谈到过[心流](http://www.wwei10.com/arts/2020/06/30/procreate/)，感兴趣可以看看。想戒游戏，通过让游戏变得没有吸引力是很难的，我个人比较推荐从其他方面入手。

洗碗，运动是我不太享受的事情，一个让它们变得有吸引力的方法是，在洗碗，运动的时候听audible或者youtube，这让我有时候甚至会期待洗碗或者运动。

### 让行动轻而易举。

> Make it easy / make it difficult

每一天中都有一些很重要的选择，通过做这些选择来限制未来的行为。
- 没有WFH的时候，选择早上去公司上班而不是在家工作就给一天定了性。选择了在家工作，大概率就要点外卖，吃的相对不健康。家里分心的事情很多，工作效率不佳。看到游戏机说不定就会打几个小时游戏。
- 给游戏加上parental control，只能晚上6点以后12点以前以及周末可以玩，且最多玩10个小时。
- 运用这个原理，加上我最近在学swift编程，我开发了个[网站封锁工具](https://github.com/wwei10/BlockSite)，代码在[GitHub](https://github.com/wwei10/BlockSite)上。功能相当基础，按一个按钮，封锁所有网站30分钟。我顺带着还玩了下google cloud，搞了个[倒计时页面](https://blocksite-gcloud-app.wl.r.appspot.com)来做重定向。很有意思的一个项目。


### 让奖赏令人满意。

> Make it satisfying / make it unsatisfying

习惯日志是个让人获得满足感的好办法。市面上app很多，试过streaks，habitica，最终还是觉得用纸+bullet journal的形式最合我胃口。贴个我的习惯日志供大家参考。

![Atomic Habits](/images/habit-tracker.jpg)


## 后记

我是在audible上听完了这本书，推荐！


----------------

## 附录

Swift开发相当有趣，就是一开始code signing各种有问题，`codesign -vvvv <filepath>`帮了大忙。有些时候safari extension不会自动重载。必须要手动clear build，rebuild，再运行。另一个比较坑的地方是safari不支持beforeload trigger，也花了我不少时间。