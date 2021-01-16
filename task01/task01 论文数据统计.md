# task01 论文数据统计

## 1.1 任务说明

- 任务主题：论文数量统计，即统计2019年全年计算机各个方向论文数量；
- 任务内容：赛题的理解、使用 **Pandas** 读取数据并进行统计；
- 任务成果：学习 **Pandas** 的基础操作；
- 可参考的学习资料：[开源组织Datawhale joyful-pandas项目](https://github.com/datawhalechina/joyful-pandas)

## 1.2 数据集介绍

- 数据集来源：[数据集链接](https://www.kaggle.com/Cornell-University/arxiv)；
- 数据集的格式如下：
  - `id`：arXiv ID，可用于访问论文；
  - `submitter`：论文提交者；
  - `authors`：论文作者；
  - `title`：论文标题；
  - `comments`：论文页数和图表等其他信息；
  - `journal-ref`：论文发表的期刊的信息；
  - `doi`：数字对象标识符，[https://www.doi.org](https://www.doi.org/)；
  - `report-no`：报告编号；
  - `categories`：论文在 arXiv 系统的所属类别或标签；
  - `license`：文章的许可证；
  - `abstract`：论文摘要；
  - `versions`：论文版本；
  - `authors_parsed`：作者的信息。

## 1.3 arxiv论文类别介绍

我们从arxiv官网，查询到论文的类别名称以及其解释如下。

链接：https://arxiv.org/help/api/user-manual 的 5.3 小节的 Subject Classifications 的部分，或 https://arxiv.org/category_taxonomy， 具体的153种paper的类别部分如下：

```
'astro-ph': 'Astrophysics',
'astro-ph.CO': 'Cosmology and Nongalactic Astrophysics',
'astro-ph.EP': 'Earth and Planetary Astrophysics',
'astro-ph.GA': 'Astrophysics of Galaxies',
'cs.AI': 'Artificial Intelligence',
'cs.AR': 'Hardware Architecture',
'cs.CC': 'Computational Complexity',
'cs.CE': 'Computational Engineering, Finance, and Science',
'cs.CV': 'Computer Vision and Pattern Recognition',
'cs.CY': 'Computers and Society',
'cs.DB': 'Databases',
'cs.DC': 'Distributed, Parallel, and Cluster Computing',
'cs.DL': 'Digital Libraries',
'cs.NA': 'Numerical Analysis',
'cs.NE': 'Neural and Evolutionary Computing',
'cs.NI': 'Networking and Internet Architecture',
'cs.OH': 'Other Computer Science',
'cs.OS': 'Operating Systems',
```

## 1.4 具体代码实现以及讲解

### 1.4.1 导入package并读取原始数据

``` python
import seaborn as sns  #用于画图
from bs4 import BeautifulSoup  #用于爬取arxiv的数据
import re  #用于正则表达式，匹配字符串的模式
import requests  #用于网络连接，发送网络请求，使用域名获取对应信息
import json  #读取数据，我们的数据为json格式的
import pandas as pd  #数据处理，数据分析
import matplotlib.pyplot as plt  #画图工具
```

**Seaborn:** 基于matplotlib的图形可视化python包，它是在matplotlib的基础上进行了更高级的API封装，从而使得作图更加容易，在大多数情况下使用seaborn能做出很具有吸引力的图，而使用matplotlib就能制作出具有更多特色的图。

``` python
# 读入数据
data  = []

with open("arxiv-metadata-oai-snapshot.json", 'r') as f: 
    for line in enumerate(f): 
      
        data.append(json.loads(line))
        
data = pd.DataFrame(data) 
data.shape
# (1778381, 14)
```

`json.loads( )`:将json字符串转换成字典格式

`json.dumps( )`:将字典格式数据转换为json格式

``` python
data.head()
```

|      |        id |          submitter |                                           authors |                                             title |                                comments |                               journal-ref |                        doi |        report-no |      categories |                                           license |                                        abstract |                                          versions | update_date |                                    authors_parsed |
| ---: | --------: | -----------------: | ------------------------------------------------: | ------------------------------------------------: | --------------------------------------: | ----------------------------------------: | -------------------------: | ---------------: | --------------: | ------------------------------------------------: | ----------------------------------------------: | ------------------------------------------------: | ----------: | ------------------------------------------------: |
|    0 | 0704.0001 |     Pavel Nadolsky | C. Bal\'azs, E. L. Berger, P. M. Nadolsky, C.-... | Calculation of prompt diphoton production cros... | 37 pages, 15 figures; published version |                  Phys.Rev.D76:013009,2007 | 10.1103/PhysRevD.76.013009 | ANL-HEP-PR-07-12 |          hep-ph |                                              None | A fully differential calculation in perturba... | [{'version': 'v1', 'created': 'Mon, 2 Apr 2007... |  2008-11-26 | [[Balázs, C., ], [Berger, E. L., ], [Nadolsky,... |
|    1 | 0704.0002 |       Louis Theran |                   Ileana Streinu and Louis Theran |          Sparsity-certifying Graph Decompositions |   To appear in Graphs and Combinatorics |                                      None |                       None |             None |   math.CO cs.CG | http://arxiv.org/licenses/nonexclusive-distrib... | We describe a new algorithm, the (𝑘,ℓ)(k,ℓ)-... | [{'version': 'v1', 'created': 'Sat, 31 Mar 200... |  2008-12-13 |          [[Streinu, Ileana, ], [Theran, Louis, ]] |
|    2 | 0704.0003 |        Hongjun Pan |                                       Hongjun Pan | The evolution of the Earth-Moon system based o... |                     23 pages, 3 figures |                                      None |                       None |             None |  physics.gen-ph |                                              None | The evolution of Earth-Moon system is descri... | [{'version': 'v1', 'created': 'Sun, 1 Apr 2007... |  2008-01-13 |                                [[Pan, Hongjun, ]] |
|    3 | 0704.0004 |       David Callan |                                      David Callan | A determinant of Stirling cycle numbers counts... |                                11 pages |                                      None |                       None |             None |         math.CO |                                              None | We show that a determinant of Stirling cycle... | [{'version': 'v1', 'created': 'Sat, 31 Mar 200... |  2007-05-23 |                               [[Callan, David, ]] |
|    4 | 0704.0005 | Alberto Torchinsky |          Wael Abu-Shammala and Alberto Torchinsky |               From dyadic Λ𝛼Λα to $\Lambda_{\a... |                                    None | Illinois J. Math. 52 (2008) no.2, 681-689 |                       None |             None | math.CA math.FA |                                              None | In this paper we show how to compute the $\L... | [{'version': 'v1', 'created': 'Mon, 2 Apr 2007... |  2013-10-15 | [[Abu-Shammala, Wael, ], [Torchinsky, Alberto, ]] |

``` python
def readArxivFile(path, columns=['id', 'submitter', 'authors', 'title', 'comments', 'journal-ref', 'doi',
       'report-no', 'categories', 'license', 'abstract', 'versions',
       'update_date', 'authors_parsed'], count=None):
    
    data  = []
    with open(path, 'r') as f: 
        for idx, line in enumerate(f): 
            if idx == count:
                break
                
            d = json.loads(line)
            d = {col : d[col] for col in columns}
            data.append(d)

    data = pd.DataFrame(data)
    return data

data = readArxivFile('arxiv-metadata-oai-snapshot.json', ['id', 'categories', 'update_date'])
# 这一节只需使用三列数据
```

###  1.4.2 数据预处理

首先我们先来粗略统计论文的种类信息：

``` python
data['categories'].describe()
```

``` 
count      1796911
unique       62055
top       astro-ph
freq         86914
Name: categories, dtype: object
```

``` python
data.shape #显示数据大小
# (1796911, 3)
```

以上的结果表明：共有1796911个数据，有62055个子类（因为有论文的类别是多个，例如一篇paper的类别是CS.AI & CS.MM和一篇paper的类别是CS.AI & CS.OS属于不同的子类别，这里仅仅是粗略统计），其中最多的种类是astro-ph，即Astrophysics（天体物理学），共出现了86914次。

由于部分论文的类别不止一种，所以下面我们判断在本数据集中共出现了多少种类别。

``` python
unique_categories = set([i for l in [x.split(' ') for x in data['categories']] for i in l])
unique_categories
```

```
{'acc-phys',
 'adap-org',
 'alg-geom',
 'ao-sci',
 'astro-ph',
 'astro-ph.CO',
 'astro-ph.EP',
 'astro-ph.GA',
 'astro-ph.HE',
 'astro-ph.IM',
 'astro-ph.SR',
 'atom-ph',
 'bayes-an',
 'chao-dyn',
 'chem-ph',
 'cmp-lg',
 'comp-gas',
 'cond-mat',
 'cond-mat.dis-nn',
 'cond-mat.mes-hall',
 'cond-mat.mtrl-sci',
 'cond-mat.other',
 'cond-mat.quant-gas',
 'cond-mat.soft',
 'cond-mat.stat-mech',
 'cond-mat.str-el',
 'cond-mat.supr-con',
 'cs.AI',
 'cs.AR',
 'cs.CC',
 'cs.CE',
 'cs.CG',
 'cs.CL',
 'cs.CR',
 'cs.CV',
 'cs.CY',
 'cs.DB',
 'cs.DC',
 'cs.DL',
 'cs.DM',
 'cs.DS',
 'cs.ET',
 'cs.FL',
 'cs.GL',
 'cs.GR',
 'cs.GT',
 'cs.HC',
 'cs.IR',
 'cs.IT',
 'cs.LG',
 'cs.LO',
 'cs.MA',
 'cs.MM',
 'cs.MS',
 'cs.NA',
 'cs.NE',
 'cs.NI',
 'cs.OH',
 'cs.OS',
 'cs.PF',
 'cs.PL',
 'cs.RO',
 'cs.SC',
 'cs.SD',
 'cs.SE',
 'cs.SI',
 'cs.SY',
 'dg-ga',
 'econ.EM',
 'econ.GN',
 'econ.TH',
 'eess.AS',
 'eess.IV',
 'eess.SP',
 'eess.SY',
 'funct-an',
 'gr-qc',
 'hep-ex',
 'hep-lat',
 'hep-ph',
 'hep-th',
 'math-ph',
 'math.AC',
 'math.AG',
 'math.AP',
 'math.AT',
 'math.CA',
 'math.CO',
 'math.CT',
 'math.CV',
 'math.DG',
 'math.DS',
 'math.FA',
 'math.GM',
 'math.GN',
 'math.GR',
 'math.GT',
 'math.HO',
 'math.IT',
 'math.KT',
 'math.LO',
 'math.MG',
 'math.MP',
 'math.NA',
 'math.NT',
 'math.OA',
 'math.OC',
 'math.PR',
 'math.QA',
 'math.RA',
 'math.RT',
 'math.SG',
 'math.SP',
 'math.ST',
 'mtrl-th',
 'nlin.AO',
 'nlin.CD',
 'nlin.CG',
 'nlin.PS',
 'nlin.SI',
 'nucl-ex',
 'nucl-th',
 'patt-sol',
 'physics.acc-ph',
 'physics.ao-ph',
 'physics.app-ph',
 'physics.atm-clus',
 'physics.atom-ph',
 'physics.bio-ph',
 'physics.chem-ph',
 'physics.class-ph',
 'physics.comp-ph',
 'physics.data-an',
 'physics.ed-ph',
 'physics.flu-dyn',
 'physics.gen-ph',
 'physics.geo-ph',
 'physics.hist-ph',
 'physics.ins-det',
 'physics.med-ph',
 'physics.optics',
 'physics.plasm-ph',
 'physics.pop-ph',
 'physics.soc-ph',
 'physics.space-ph',
 'plasm-ph',
 'q-alg',
 'q-bio',
 'q-bio.BM',
 'q-bio.CB',
 'q-bio.GN',
 'q-bio.MN',
 'q-bio.NC',
 'q-bio.OT',
 'q-bio.PE',
 'q-bio.QM',
 'q-bio.SC',
 'q-bio.TO',
 'q-fin.CP',
 'q-fin.EC',
 'q-fin.GN',
 'q-fin.MF',
 'q-fin.PM',
 'q-fin.PR',
 'q-fin.RM',
 'q-fin.ST',
 'q-fin.TR',
 'quant-ph',
 'solv-int',
 'stat.AP',
 'stat.CO',
 'stat.ME',
 'stat.ML',
 'stat.OT',
 'stat.TH',
 'supr-con'}
```

一共176种论文种类，比我们直接从 https://arxiv.org/help/api/user-manual 的 5.3 小节的 Subject Classifications 的部分或 https://arxiv.org/category_taxonomy中的到的类别多，这说明存在一些官网上没有的类别，这是一个小细节。不过对于我们的计算机方向的论文没有影响，依然是以下的40个类别，我们从原数据中提取的和从官网的到的种类是可以一一对应的。

我们的任务是对2019年以后的paper进行分析，首先对于时间特征进行预处理，从而得到2019年以后的所有种类的论文：

``` python
data['year'] = pd.to_datetime(data['update_date']).dt.year # 将update_date的日期转换格式并提取年份
del data['update_date']
# 之前学的是data.drop('update_date',axis=1,inplace=True)

data = data[data.year >= 2019].reset_index(drop=True)
data
```

|        |               id |                               categories | year |
| -----: | ---------------: | ---------------------------------------: | ---- |
|      0 |        0704.0297 |                                 astro-ph | 2019 |
|      1 |        0704.0342 |                                  math.AT | 2019 |
|      2 |        0704.0360 |                                 astro-ph | 2019 |
|      3 |        0704.0525 |                                    gr-qc | 2019 |
|      4 |        0704.0535 |                                 astro-ph | 2019 |
|    ... |              ... |                                      ... | ...  |
| 395118 | quant-ph/9911051 |                                 quant-ph | 2020 |
| 395119 | solv-int/9511005 |                         solv-int nlin.SI | 2019 |
| 395120 | solv-int/9809008 |                         solv-int nlin.SI | 2019 |
| 395121 | solv-int/9909010 | solv-int adap-org hep-th nlin.AO nlin.SI | 2019 |
| 395122 | solv-int/9909014 |                         solv-int nlin.SI | 2019 |

395123 rows × 3 columns

这里我们得到了所有2019年之后的论文，下面我们来爬取论文分类：

``` python
# 爬取所有的类别
website_url = requests.get('https://arxiv.org/category_taxonomy').text #获取网页的文本数据
soup = BeautifulSoup(website_url,'lxml') #爬取数据，这里使用lxml的解析器，加速
root = soup.find('div',{'id':'category_taxonomy_list'}) #找出 BeautifulSoup 对应的标签入口
tags = root.find_all(["h2","h3","h4","p"], recursive=True) #读取 tags

#初始化 str 和 list 变量
level_1_name = ""
level_2_name = ""
level_2_code = ""
level_1_names = []
level_2_codes = []
level_2_names = []
level_3_codes = []
level_3_names = []
level_3_notes = []

#进行
for t in tags:
    if t.name == "h2":
        level_1_name = t.text    
        level_2_code = t.text
        level_2_name = t.text
    elif t.name == "h3":
        raw = t.text
        level_2_code = re.sub(r"(.*)\((.*)\)",r"\2",raw) #正则表达式：模式字符串：(.*)\((.*)\)；被替换字符串"\2"；被处理字符串：raw
        level_2_name = re.sub(r"(.*)\((.*)\)",r"\1",raw)
    elif t.name == "h4":
        raw = t.text
        level_3_code = re.sub(r"(.*) \((.*)\)",r"\1",raw)
        level_3_name = re.sub(r"(.*) \((.*)\)",r"\2",raw)
    elif t.name == "p":
        notes = t.text
        level_1_names.append(level_1_name)
        level_2_names.append(level_2_name)
        level_2_codes.append(level_2_code)
        level_3_names.append(level_3_name)
        level_3_codes.append(level_3_code)
        level_3_notes.append(notes)

#根据以上信息生成dataframe格式的数据
df_taxonomy = pd.DataFrame({
    'group_name' : level_1_names,
    'archive_name' : level_2_names,
    'archive_id' : level_2_codes,
    'category_name' : level_3_names,
    'categories' : level_3_codes,
    'category_description': level_3_notes
    
})

#按照 "group_name" 进行分组，在组内使用 "archive_name" 进行排序
df_taxonomy.groupby(["group_name","archive_name"])
df_taxonomy
```

|      |       group_name |     archive_name |       archive_id |                                   category_name | categories | category_description                              |
| ---: | ---------------: | ---------------: | ---------------: | ----------------------------------------------: | ---------: | ------------------------------------------------- |
|    0 | Computer Science | Computer Science | Computer Science |                         Artificial Intelligence |      cs.AI | Covers all areas of AI except Vision, Robotics... |
|    1 | Computer Science | Computer Science | Computer Science |                           Hardware Architecture |      cs.AR | Covers systems organization and hardware archi... |
|    2 | Computer Science | Computer Science | Computer Science |                        Computational Complexity |      cs.CC | Covers models of computation, complexity class... |
|    3 | Computer Science | Computer Science | Computer Science | Computational Engineering, Finance, and Science |      cs.CE | Covers applications of computer science to the... |
|    4 | Computer Science | Computer Science | Computer Science |                          Computational Geometry |      cs.CG | Roughly includes material in ACM Subject Class... |
|  ... |              ... |              ... |              ... |                                             ... |        ... | ...                                               |
|  150 |       Statistics |       Statistics |       Statistics |                                     Computation |    stat.CO | Algorithms, Simulation, Visualization             |
|  151 |       Statistics |       Statistics |       Statistics |                                     Methodology |    stat.ME | Design, Surveys, Model Selection, Multiple Tes... |
|  152 |       Statistics |       Statistics |       Statistics |                                Machine Learning |    stat.ML | Covers machine learning papers (supervised, un... |
|  153 |       Statistics |       Statistics |       Statistics |                                Other Statistics |    stat.OT | Work in statistics that does not fit into the ... |
|  154 |       Statistics |       Statistics |       Statistics |                               Statistics Theory |    stat.TH | stat.TH is an alias for math.ST. Asymptotics, ... |

**这里我们得到了155种类别，比1.3种说好的153种又多两种啊哈？**

对比了一下数据发现，`df_taxonomy`中缺了`astro_ph`，多了`econ.GN, econ.TH, econ.SY  `？



这里说明一下，我们使用`re.sub`来用于替换字符串中的匹配项

`re.sub(pattern, repl, string, count=0, flags=0)`：substitute，替换。其中的参数为：

- pattern : 正则中的模式字符串。
- repl : 替换之后的字符串，也可为一个函数。
- string : 原始字符串。
- count : 模式匹配后替换的最大次数，默认 0 表示替换所有的匹配。
- flags : 编译时用的匹配模式，数字形式。
- 其中pattern、repl、string为必选参数

举例：

``` python
import re

phone = "2004-959-559 # 这是一个电话号码"

# 删除注释
num = re.sub(r'#.*$', "", phone)
print ("电话号码 : ", num)
 
# 移除非数字的内容
num = re.sub(r'\D', "", phone)
print ("电话号码 : ", num)
```

``` 
电话号码 :  2004-959-559 
电话号码 :  2004959559
```

对于我们的代码而言

``` python
re.sub(r"(.*)\((.*)\)",r"\2",raw)

#raw = Astrophysics(astro-ph)
#output = astro-ph
```

+ pattern: "任意字符" + "(" + "任意字符" + ")"
+ repl: 第2个分组的内容
+ string:原始的爬取的数据

###  1.4.3 数据分析及可视化

接下来我们首先看一下所有大类的paper数量分布：

由于在本次task中我们只使用了数据集中的`id,categories,update_year`三列数据，因此需要与arXiv中的数据进行对比，匹配分类。

感觉原操作中，得到分类之后的论文数量少了许多，在此稍作改进。

**原操作：**

``` python
_df = data.merge(df_taxonomy, on="categories", how="left").drop_duplicates(["id","group_name"]).groupby("group_name").agg({"id":"count"}).sort_values(by="id",ascending=False).reset_index()

_df
```

|      |                                 group_name |    id |
| ---: | -----------------------------------------: | ----: |
|    0 |                                    Physics | 79985 |
|    1 |                                Mathematics | 51567 |
|    2 |                           Computer Science | 40067 |
|    3 |                                 Statistics |  4054 |
|    4 | Electrical Engineering and Systems Science |  3297 |
|    5 |                       Quantitative Biology |  1994 |
|    6 |                       Quantitative Finance |   826 |
|    7 |                                  Economics |   576 |

这样得到的论文总数只有182366，而数据集中2019年后的所有论文数量有395123。我们将原操作拆开：

``` python
merge_df = data.merge(df_taxonomy, on="categories", how="left")
merge_df
```

|      |        id | categories | year |  group_name |                             archive_name |  archive_id |                            category_name | category_description                              |
| ---: | --------: | ---------: | ---: | ----------: | ---------------------------------------: | ----------: | ---------------------------------------: | ------------------------------------------------- |
|    0 | 0704.0297 |   astro-ph | 2019 |         NaN |                                      NaN |         NaN |                                      NaN | NaN                                               |
|    1 | 0704.0342 |    math.AT | 2019 | Mathematics |                              Mathematics | Mathematics |                       Algebraic Topology | Homotopy theory, homological algebra, algebrai... |
|    2 | 0704.0360 |   astro-ph | 2019 |         NaN |                                      NaN |         NaN |                                      NaN | NaN                                               |
|    3 | 0704.0525 |      gr-qc | 2019 |     Physics | General Relativity and Quantum Cosmology |       gr-qc | General Relativity and Quantum Cosmology | Description coming soon                           |
|    4 | 0704.0535 |   astro-ph | 2019 |         NaN |                                      NaN |         NaN |                                      NaN | NaN                                               |
|  ... |       ... |        ... |  ... |         ... |                                      ... |         ... |                                      ... | ...                                               |

395123 rows × 8 columns

merge之后数据集中存在大量数据无法和arXiv中的分类匹配上，我们把它们筛选出来看一下。

``` python
merge_df[merge_df.group_name.isnull()]
```

|        |               id |                               categories | year | group_name | archive_name | archive_id | category_name | category_description |
| -----: | ---------------: | ---------------------------------------: | ---: | ---------: | -----------: | ---------: | ------------: | -------------------- |
|      0 |        0704.0297 |                                 astro-ph | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |
|      2 |        0704.0360 |                                 astro-ph | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |
|      4 |        0704.0535 |                                 astro-ph | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |
|     11 |        0704.1245 |                                 astro-ph | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |
|     13 |        0704.1403 |                  math.QA math.DG math.SG | 2020 |        NaN |          NaN |        NaN |           NaN | NaN                  |
|    ... |              ... |                                      ... |  ... |        ... |          ... |        ... |           ... | ...                  |
| 395117 | quant-ph/9910035 |        quant-ph cond-mat math-ph math.MP | 2020 |        NaN |          NaN |        NaN |           NaN | NaN                  |
| 395119 | solv-int/9511005 |                         solv-int nlin.SI | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |
| 395120 | solv-int/9809008 |                         solv-int nlin.SI | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |
| 395121 | solv-int/9909010 | solv-int adap-org hep-th nlin.AO nlin.SI | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |
| 395122 | solv-int/9909014 |                         solv-int nlin.SI | 2019 |        NaN |          NaN |        NaN |           NaN | NaN                  |

212757 rows × 8 columns

这其中有部分是类别未在arXiv中收录，有部分是由于有多种类别，因此，我准备将`data`中`categories`拆分成多行数据，多类别的论文在每类别都计数。

**我的操作：**

``` python
data = data['categories'].str.split(' ',expand=True).stack().reset_index(level=0).set_index('level_0').rename(columns={0:'categories'}).join(data.drop('categories',axis=1)
data                                                                                                                                             
```

|        | categories |               id | year |
| -----: | ---------: | ---------------: | ---- |
|      0 |   astro-ph |        0704.0297 | 2019 |
|      1 |    math.AT |        0704.0342 | 2019 |
|      2 |   astro-ph |        0704.0360 | 2019 |
|      3 |      gr-qc |        0704.0525 | 2019 |
|      4 |   astro-ph |        0704.0535 | 2019 |
|    ... |        ... |              ... | ...  |
| 395121 |     hep-th | solv-int/9909010 | 2019 |
| 395121 |    nlin.AO | solv-int/9909010 | 2019 |
| 395121 |    nlin.SI | solv-int/9909010 | 2019 |
| 395122 |   solv-int | solv-int/9909014 | 2019 |
| 395122 |    nlin.SI | solv-int/9909014 | 2019 |

726715 rows × 3 columns

（数量翻倍了，芜湖）

``` python
# 再merge
merge_ = data.merge(df_taxonomy, on="categories", how="left")

#看一下现在还有多少不能对上的
merge_[merge_.group_name.isnull()]
```

|        | categories |               id | year | group_name | archive_name | archive_id | category_name | category_description |
| -----: | ---------: | ---------------: | ---: | ---------: | -----------: | ---------: | ------------: | -------------------: |
|      0 |   astro-ph |        0704.0297 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
|      2 |   astro-ph |        0704.0360 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
|      4 |   astro-ph |        0704.0535 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
|     11 |   astro-ph |        0704.1245 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
|     16 |   astro-ph |        0704.1430 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
|    ... |        ... |              ... |  ... |        ... |          ... |        ... |           ... |                  ... |
| 726704 |   solv-int | solv-int/9511005 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
| 726706 |   solv-int | solv-int/9809008 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
| 726708 |   solv-int | solv-int/9909010 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
| 726709 |   adap-org | solv-int/9909010 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |
| 726713 |   solv-int | solv-int/9909014 | 2019 |        NaN |          NaN |        NaN |           NaN |                  NaN |

稍微翻了一下，其实是可以对上的，属于小类中的小类，对照网站找了找，有几个还是没收录。那没办法了。

``` python
set(merge_[merge_.group_name.isnull()]['categories'])
```

```python
{'acc-phys',		# physics
 'adap-org',    # nlin > nlin.AO
 'alg-geom',		#	math > math.A
 'astro-ph',		# astro
 'chao-dyn',		# nlin > nlin.CD
 'chem-ph',			# physics > physics.chem-ph
 'cmp-lg',			# cs > cs.CL
 'comp-gas',		# nlin > nlin.CG
 'cond-mat',		# cond-mat   没有
 'dg-ga',				# math > math.DG
 'funct-an',		# math > math.FA
 'mtrl-th',			# cond-mat   没有
 'patt-sol',		# nlin > nlin.PS
 'plasm-ph',		# physics
 'q-alg',				# math > math.QA
 'q-bio',				# q-bio
 'solv-int',		# nlin > nlin.S
 'supr-con'}		# cond-mat  没有
```

``` python
# 手动一个一个修改。。。数据量再大一点可能就不想搞了
merge_.loc[merge_['categories']=='acc-phys','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='adap-org','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='alg-geom','group_name'] = 'Mathematics'
merge_.loc[merge_['categories']=='astro-ph','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='chao-dyn','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='chem-ph','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='cmp-lg','group_name'] = 'Computer Science'
merge_.loc[merge_['categories']=='comp-gas','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='dg-ga','group_name'] = 'Mathematics'
merge_.loc[merge_['categories']=='funct-an','group_name'] = 'Mathematics'
merge_.loc[merge_['categories']=='patt-sol','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='plasm-ph','group_name'] = 'Physics'
merge_.loc[merge_['categories']=='q-alg','group_name'] = 'Mathematics'
merge_.loc[merge_['categories']=='solv-int','group_name'] = 'Physics'
```

``` python
# 这里由于有的论文是跨领域的，干脆全都算一篇，但这样得到的总数比较大
# 统计总数
merge_.drop_duplicates(["id","group_name","categories"]).groupby("group_name").agg({"id":"count"}).sort_values(by="id",ascending=False).reset_index()
```

|      |                                 group_name |     id |
| ---: | -----------------------------------------: | -----: |
|    0 |                                    Physics | 279390 |
|    1 |                           Computer Science | 198558 |
|    2 |                                Mathematics | 160444 |
|    3 |                                 Statistics |  45720 |
|    4 | Electrical Engineering and Systems Science |  25995 |
|    5 |                       Quantitative Biology |   9152 |
|    6 |                       Quantitative Finance |   4638 |
|    7 |                                  Economics |   2716 |

``` python
fig = plt.figure(figsize=(15,12))
explode = (0, 0, 0, 0.2, 0.3, 0.3, 0.2, 0.1) 
plt.pie(merge_["id"],  labels=merge_["group_name"], autopct='%1.2f%%', startangle=160, explode=explode)
plt.tight_layout()
plt.show()
```

![image](https://github.com/Mounvo/DW_dataAnalysis_academicTrends/blob/main/picture/task1_papercount_bar.png)

下面统计在计算机各个子领域2019年后的paper数量，我们同样使用 merge 函数，对于两个dataframe 共同的特征 categories 进行合并并且进行查询。然后我们再对于数据进行统计和排序从而得到以下的结果：

``` python
cats = data.merge(df_taxonomy, on="categories")
cats.loc[cats['categories']=='cmp-lg','group_name'] = 'Computer Science'
cats = cats.query("group_name == 'Computer Science'")
cats.groupby(["year","category_name"]).count().reset_index().pivot(index="category_name", columns="year",values="id")
```

|                                            year |  2019 |  2020 |
| ----------------------------------------------: | ----: | ----: |
|                                   category_name |       |       |
|                         Artificial Intelligence |  4723 |  7541 |
|                        Computation and Language |  5117 |  7484 |
|                        Computational Complexity |   824 |  1089 |
| Computational Engineering, Finance, and Science |   449 |   767 |
|                          Computational Geometry |   592 |   837 |
|                Computer Science and Game Theory |   901 |  1305 |
|         Computer Vision and Pattern Recognition | 11168 | 16313 |
|                           Computers and Society |  1436 |  2446 |
|                       Cryptography and Security |  2931 |  4125 |
|                  Data Structures and Algorithms |  2131 |  2857 |
|                                       Databases |   717 |   986 |
|                               Digital Libraries |   336 |   460 |
|                            Discrete Mathematics |  1141 |  1384 |
|    Distributed, Parallel, and Cluster Computing |  2025 |  2710 |
|                           Emerging Technologies |   404 |   483 |
|            Formal Languages and Automata Theory |   473 |   566 |
|                              General Literature |    29 |    27 |
|                                        Graphics |   490 |   748 |
|                           Hardware Architecture |   243 |   500 |
|                      Human-Computer Interaction |  1392 |  1995 |
|                           Information Retrieval |  1608 |  2092 |
|                              Information Theory |  3568 |  4501 |
|                       Logic in Computer Science |  1384 |  1697 |
|                                Machine Learning | 17372 | 28936 |
|                           Mathematical Software |   149 |   252 |
|                              Multiagent Systems |   674 |  1083 |
|                                      Multimedia |   488 |   688 |
|            Networking and Internet Architecture |  1874 |  2217 |
|               Neural and Evolutionary Computing |  1501 |  2026 |
|                              Numerical Analysis |  1834 |  4629 |
|                               Operating Systems |    91 |    95 |
|                          Other Computer Science |   126 |   120 |
|                                     Performance |   395 |   513 |
|                           Programming Languages |   692 |   841 |
|                                        Robotics |  2631 |  4324 |
|                 Social and Information Networks |  1798 |  2639 |
|                            Software Engineering |  1161 |  1585 |
|                                           Sound |  1175 |  2058 |
|                            Symbolic Computation |   200 |   251 |
|                             Systems and Control |  2414 |  4728 |

得到的结果是，Machine Learning是2019年和2020年被paper设置最多的分类，2019年有17372篇CS论文与Machine Learning相关，2020年有28936篇。

