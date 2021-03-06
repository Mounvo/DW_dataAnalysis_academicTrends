# DW_dataAnalysis_academicTrends
## DW2021年1月组队学习 零基础入门数据分析-学术前沿趋势分析

[开源内容](https://github.com/datawhalechina/team-learning-data-mining/tree/master/AcademicTrends)
## 赛题背景
本次新人赛是Datawhale与天池联合发起的0基础入门系列赛事第五场 —— 零基础入门数据分析之学术前沿趋势分析。

赛题以数据分析为背景，要求选手使用公开的arXiv论文完成对应的数据分析操作。与之前的数据挖掘赛题不同，本次赛题不仅要求选手对数据进行建模，而且需要选手利用赛题数据完成具体的可视化分析。

为更好的引导大家入门，我们为本赛题定制了学习方案和学习任务，其中包括数据科学库使用（Pandas、Numpy和Matplotlib）、数据分析介绍和数据分析工具使用三部分。在具体任务中我们将讲解具体工具和使用和完成任务的过程。

通过对本方案的完整学习，可以帮助掌握数据分析基本技能。同时我们也将提供专属的视频直播学习通道。

## 任务安排
### Task00：熟悉规则（1天）
组队、修改群昵称。
熟悉打卡规则。
### Task1：论文数据统计（3天）[Link](https://github.com/datawhalechina/team-learning-data-mining/blob/master/AcademicTrends/Task1%20%E8%AE%BA%E6%96%87%E6%95%B0%E6%8D%AE%E7%BB%9F%E8%AE%A1.md)
学习主题：论文数量统计（数据统计任务），统计2019年全年，计算机各个方向论文数量；
学习内容：赛题理解、Pandas读取数据、数据统计 ；
学习成果：学习Pandas基础；
### Task2：论文作者统计（3天）[Link](https://github.com/datawhalechina/team-learning-data-mining/blob/master/AcademicTrends/Task2%20%E8%AE%BA%E6%96%87%E4%BD%9C%E8%80%85%E7%BB%9F%E8%AE%A1.md)
学习主题：论文作者统计（数据统计任务），统计所有论文作者出现评率Top10的姓名；
学习内容：作者姓名识别和统计；
学习成果：学习字符串基本操作、Matplotlib基础使用、Seaborn基础使用；
### Task3：论文代码统计（3天）[Link](https://github.com/datawhalechina/team-learning-data-mining/blob/master/AcademicTrends/Task3%20%E8%AE%BA%E6%96%87%E4%BB%A3%E7%A0%81%E7%BB%9F%E8%AE%A1.md)
学习主题：论文代码统计（数据统计任务），统计所有论文类别下包含源代码论文的比例；
学习内容：代码链接识别和统计；
学习成果：学会使用正则表达式；
### Task4：论文种类分类（3天）[Link](https://github.com/datawhalechina/team-learning-data-mining/blob/master/AcademicTrends/Task4%20%E8%AE%BA%E6%96%87%E7%A7%8D%E7%B1%BB%E5%88%86%E7%B1%BB.md)
学习主题：论文种类分类（数据建模任务），利用已有数据建模，对新论文进行类别分类；
学习内容：使用论文标题完成类别分类；
学习成果：学会文本分类的基本方法、TFIDF等；
### Task5：作者信息关联（3天）[Link](https://github.com/datawhalechina/team-learning-data-mining/blob/master/AcademicTrends/Task5%20%E4%BD%9C%E8%80%85%E4%BF%A1%E6%81%AF%E5%85%B3%E8%81%94.md)
学习主题：作者信息关联（数据建模任务），对论文作者关系进行建模，统计最常出现的作者关系；
学习内容：构建作者关系图，挖掘作者关系；
学习成果：论文作者知识图谱、图关系挖掘；

## 赛题数据
### 数据说明
arXiv 重要的学术公开网站，也是搜索、浏览和下载学术论文的重要工具。arXiv论文涵盖的范围非常广，涉及物理学的庞大分支和计算机科学的众多子学科，如数学、统计学、电气工程、定量生物学和经济学等等。

本次赛题将使用arXiv在公开的170万篇论文数据集，希望各位选手通过数据分析能够挖掘出最近学术的发展趋势和学术关键词。

数据集来源： https://www.kaggle.com/Cornell-University/arxiv
