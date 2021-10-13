# ICPC Management System

[评测地址](https://acm.sjtu.edu.cn/OnlineJudge/problem?problem_id=1347)

(当前文档正处于，并将长期处于非正式版本，因此仅供参考，对此作业的任何疑问欢迎提出issue)

## 0 前置作业

对于 B 班同学，在完成本作业之前，你应当先完成前置作业 [聪老师不想摸鱼](https://acm.sjtu.edu.cn/OnlineJudge/problem?problem_id=1383) 和 [Michelle的学生会工作](https://github.com/ACM-Programming-2021/ICPC-Management-System-2021/tree/master/Michelle) 以熟悉结构体与部分`stl`容器的使用。

## 1 背景

​	**ICPC** （国际大学生程序设计竞赛）由 ICPC 基金会举办的一项旨在展示大学生创新能力、团队精神和在压力下编写程序、分析和解决问题能力的年度竞赛，是最具影响力的大学生计算机竞赛。在 ICPC 比赛中，每个队伍尝试在错误提交次数最少的情况下解答出最多的问题，获胜者为正确解答题目最多且总罚时最少的队伍。

## 2 大作业目的

​	学会使用STL库；加深对于字符串的掌握；学会处理边界情况；提高模拟水平；加深对于时间复杂度的理解；

## 3 任务描述

​	本次大作业需要实现一个 ICPC 比赛后台系统，通过队伍的提交情况对比赛结果进行维护。具体需要实现的操作如下：

- 添加队伍
- 开始比赛
- 提交题目
- 刷新榜单
- 封榜
- 滚榜
- 查询队伍排名
- 查询队伍提交情况
- 结束比赛

我们在此对以上涉及到的术语进行解释：

队伍：每一支参赛队伍都有自己唯一的队伍名称，队名格式为20个字符内（含）的大小写字母、数字及下划线的组合。

榜单：按照排名从小到大显示每支队伍的状态。

封榜：封榜后，对于任意一支队伍，**所有封榜前该队伍未通过的题目**，封榜后的排行榜上并不显示实时的提交结果，而只显示出封榜时间段内该队伍对题目的提交次数，封榜后至少有一次提交的题目将变成冻结状态。（封榜前通过的题目，封榜后再次提交不会被冻结）

滚榜：滚榜环节中，首先在排行榜上选择一支排名最靠后且有冻结状态题目的队伍，并选择该队伍编号最小的一道冻结状态的题目进行解冻，显示该队伍对这道题目的所有提交结果（无需输出），并重新计算排名，更改排行榜上的排名状态。之后重复这项操作直到所有队伍都不存在仍处于冻结状态的题目。这样，我们就得到了当前正确的榜单。

提交：队伍提交一份解答，经评测机评测后，后台获得这次提交的基本信息。未封榜时的提交会实时更新队伍的状态（如通过题目数量等），但**并不会**更新榜单上队伍的排名。

刷新榜单：更新榜单上队伍的排名。

排名：比赛的排名由通过的题目数量决定，通过的题数多者排名靠前。当通过 的题数相同时，则比较两队间的罚时 (P = 20X + T ，X 为第一次正确提交 前的错误提交次数，T 为通过时间，即第一次正确提交的时间)，罚时小者排名靠前。依旧平局时，我们比较两队通过时间最大的题目的通过时间，最大通过时间较小的一队排名靠前，相同则比较通过时间第二大的题目，第三大的题目，以此类推。如果依旧平局则比较两支队伍名称的字典序，字典序较小的队伍排名靠前。注意，以上所有因素均不包含冻结状态的题目，显而易见，封榜后直到滚榜前排行榜上的排名不会发生改变。

## 4 具体内容

- 对于每一个命令，在输出完成后需要换行
- 输入格式参照代码框中格式，全大写部分代表命令，小写部分为对应参数
- 保证在开始比赛之前不会出现`ADDTEAM`**以外**的操作
- 保证命令格式合法（但不保证命令所执行的内容合法，具体可参见下文）

### 输出榜单

​	此部分并无直接命令，但其它命令输出榜单时采用此格式输出。

​	输出N（队伍总数）行，每行以

	队名 排名 解题数 总罚时 A B C ...

的格式表示一支队伍的情况，其中“A B C ...”表示每一题的情况，有以下三种可能：
    
    +x 该题未被冻结且已经通过。x表示该题第一次通过前错误的次数，如果x为0，则显示“+”而不是“+0”。
    
    -x 该题未被冻结但没有通过。x表示该题错误的次数，如果x为0，则显示“.”而不是“-0”。
    
    -x/y 该题被冻结。x表示封榜前错误的次数，y表示封榜后的提交次数。如果x为0，则显示“0/y”而不是“-0/y”。；

### 添加队伍

```
ADDTEAM team_name
```

​	向系统中添加队伍，成功添加输出`[Info]Add successfully.` ，若比赛已开始，输出`[Error]Add failed: competition has started.`；若比赛未开始但队伍名重复，输出`[Error]Add failed: duplicated team name.`

### 开始比赛

```
START DURATION duration_time PROBLEM problem_count
```

​	开始比赛，比赛时间 count 为闭区间 [1, duration_time] ，题号范围为前 problem_count 个大写英文字母。成功开始则输出`[Info]Competition starts.` ，若比赛已开始，输出`[Error]Start failed: competition has started.`。保证以下全部操作均在开始比赛之后出现。

### 提交题目

```
SUBMIT problem_name BY team_name WITH submit_status AT time
```

​	保证输入合法，记录 team_name 在 time 时刻对题目 problem_name 的一次提交，评测状态为 submit_status 。 submit_status 可能有以下情况：

- Accepted
- Wrong_Answer
- Runtime_Error
- Time_Limit_Exceed

除了 Accepted 算做通过外，剩下的状态均不算做通过。保证 time 按照提交出现的顺序单调上升。

### 刷新榜单

```
FLUSH
```

​	刷新当前榜单。输出`[Info]Flush scoreboard.`。


### 封榜

```
FREEZE
```

进行封榜操作。成功则输出`[Info]Freeze scoreboard.`；若已封榜但未滚榜，则输出`[Error]Freeze failed: scoreboard has been frozen.`

### 滚榜

```
SCROLL
```

​	刷新当前榜单并进行滚榜操作。成功则输出`[Info]Scroll scoreboard.`，换行后首先输出滚榜前的榜单，接着每一行输出每一次滚榜时**导致排名变化**的解冻，以

	team_name1 team_name2 solved_number penalty_time

的格式输出。 team_name1 表示题目解冻导致排名上升的队伍， team_name2 表示排名被 team_name1 取代的队伍， solved_number 和 penalty_time 为 team_name1 新的解题数和罚时；最后输出滚榜后的榜单。

​	若未封榜，则输出`[Error]Scroll failed: scoreboard has not been frozen.`

### 查询队伍排名

```
QUERY_RANKING team_name
```

​	查询对应队伍排名。若队伍不存在，则输出`[Error]Query ranking failed: cannot find the team.`。
成功则输出`[Info]Complete query ranking.`。此时若处在封榜状态，则需要输出一行`[Warning]Scoreboard is frozen. The ranking may be inaccurate until it were scrolled. `；然后不论是否封榜，输出一行，以

	team_name NOW AT RANKING ranking

的格式输出上一次刷新榜单后的队伍排名。ranking为查询得到的队伍排名。

### 查询队伍提交情况

```
QUERY_SUBMISSION team_name WHERE PROBLEM=problem_name AND STATUS=status
```

​	查询对应队伍的满足条件的最后一次提交。可参考以下合法示例：

```SQL
QUERY_SUBMISSION Team_Rocket WHERE PROBLEM=ALL AND STATUS=ALL//查询Team_Rocket的最后一次提交
QUERY_SUBMISSION Team_Plasma WHERE PROBLEM=ALL AND STATUS=Accepted// 查询Team_Plasma的最后一次状态为Accepted的提交
QUERY_SUBMISSION Pokemon_League WHERE PROBLEM=A AND STATUS=ALL // 查询Pokemon_League的最后一次对题目A的提交
QUERY_SUBMISSION Opelucid_Gym WHERE PROBLEM=M AND STATUS=Runtime_Error //查询Opelucid_Gym队的最后一次对M题状态为Runtime_Error的提交
```
成功则输出`[Info]Complete query submission.`。若无满足条件的提交，输出`Cannot find any submission.`，否则输出一行表示最后一次满足条件的提交，以

	team_name problem_name status time

的格式输出。 problem_name 为该次提交的题目编号， status 为该次提交的状态， time 为该次提交的时间。保证询问中的 problem_name 与 status 合法。

若队伍不存在，则输出`[Error]Query submission failed: cannot find the team.`。


### 结束比赛

```
END
```

​	结束比赛。需输出`[Info]Competition ends.`保证结束比赛时排行榜不处于封榜状态，且之后没有任何操作。

## 5 数据范围

对于60%的数据，队伍总数`N`<=500，操作次数`opt`<=10000。

对于100%的数据，队伍总数`N`<=10000，题目总数`M`<=26，比赛时长`T`<=100000，操作次数`opt`<=300000，刷新榜单次数`opt_flush`<=1000。封榜次数`opt_freeze`<=10。

本目录下`data`文件夹中的数据并非 Online Judge 上的测试数据点，本地通过所有数据点并不能保证你能够通过 Online Judge 上的测试。每位同学有**一次**机会从 TA 获得一个测试数据点，**同时禁止与同学分享你获得的数据点**。

## 6 评分标准

A班：

* 前置作业 - 10%
* icpc - 70%
* code review - 20%

B班：

* STL练习 - 5%
* 前置作业 - 15%
* icpc - 60%
* code review - 20%

在code review中会**严格审查代码风格**。 TA 建议使用[谷歌标准](https://zh-google-styleguide.readthedocs.io/en/latest/)或[ISO标准](https://isocpp.org/wiki/faq/coding-standards)，你也可以选择其他标准或使用你自己的标准。如果你没有使用 TA 建议的标准，请在 code review 时展示你参考的风格指南。如果使用自己的标准，请**在开始完成作业之前先完成你自己的风格指南**，并全程参考完成代码。

## 7 截止时间

STL练习：第五周周日（10月17日）23:00

前置作业：第六周周三（10月20日）23:00

icpc：第七周周日（10月31日）23:00

## 8 Q&A

- 提交时间会有相同的吗？
  
  会，单调上升不是严格的，可能出现平台。

- 滚榜时每次选择队伍依照的排名是动态变化的吗？
  
  是的。

- 封榜后的提交可以被查询提交情况查询到吗？

  可以的。

- 第一次刷新榜单前的排名的依据是什么？

  队伍名称的字典序。

- 封榜时刻的榜单是最后一次刷新的榜单还是封榜时刻的榜单？

  封榜时刻的榜单。榜单的排名和队伍信息相互独立，未封榜时的提交虽然不会更新队伍的排名，但通过题目数量、提交次数等均会实时更新。
