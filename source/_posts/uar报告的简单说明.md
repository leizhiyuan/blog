title: UAR报告的简单说明
tags:
  - 大学
  - 学习
  - 总结
  - 编程
id: 766
categories:
  - 学习笔记
date: 2012-05-29 08:15:43
---

### UAR报告由以下几个部分构成。就这个例子简单说一下。

### **Example UAR — Time Zone ListBox Is Not Good  //标题**

<div>

#### **UAR Identifier   //问题编号，从1开始，每个问题都这样的格式来说明，就构成了UAR报告**

HE18—Problem  //后面这个problem表示有问题，也可以是Good，表示这部分很好。没问题。

#### **Succinct description: //简短的描述**

Time Zone pull-down ListBox provides too much irrelevant information.

#### **Evidence for the aspect:  //违反了哪条规则，共有十条规则。**

Heuristic: Aesthetics and minimalist design

**Interface aspect: **

The pull-down ListBox has 50 lines of information—in very small font. There are many competing items of information to visually search, the vast majority of which are irrelevant to any one user's particular task of finding a single desired time zone

&nbsp;

<<<<<<< HEAD
<center style="text-align: -webkit-auto;"><span style="font-size: small;"><span style="line-height: normal;">[![]({{BASE_PATH}}/images/ "uar")](http://seqcc.icarnegie.com/content/SSD/SSD4/3.1/normal/pg-creatng-evaltng-interfaces/pg-crt-eval-list-combo/pg-he-clean-beauty/Ex5.png)</span></span></center>In addition, the information is structured as follows:The string "[GMT " begins each line (which means Greenwich Mean Time). If the time zone is behind GMT, then a "-" indicates this fact, which is followed by the number of hours and minutes the time zone is behind GMT. If the time zone is ahead of GMT, a "+" is used instead. Finally, some words follow the GMT offset, which are either city or country names, or the names of regions.&nbsp;
=======
<center style="text-align: -webkit-auto;"><span style="font-size: small;"><span style="line-height: normal;">[![](/images/ "uar")](http://seqcc.icarnegie.com/content/SSD/SSD4/3.1/normal/pg-creatng-evaltng-interfaces/pg-crt-eval-list-combo/pg-he-clean-beauty/Ex5.png)</span></span></center>In addition, the information is structured as follows:The string "[GMT " begins each line (which means Greenwich Mean Time). If the time zone is behind GMT, then a "-" indicates this fact, which is followed by the number of hours and minutes the time zone is behind GMT. If the time zone is ahead of GMT, a "+" is used instead. Finally, some words follow the GMT offset, which are either city or country names, or the names of regions.&nbsp;
>>>>>>> 换电脑之后重新备份

The presentation of this structured information violates the **aesthetics and minimalist design** heuristic because the structure is not preserved visually from item to item. For example, the words are not always lined up vertically; see the entries for:

<span>[GMT -01:00] Azores, Cape Verdi Is.</span>
<span>[GMT] Greenwich Mean Time; Dublin, Edinburgh, London, Lisbon</span>
<span>[GMT] Monrovia, Casablanca</span>
<span>[GMT +01:00] Berlin, Stockholm, Rome, Bern, Brussels, Vienna</span>

#### **Explanation of the aspect: //详细解释，说明问题**

There are about 30 distinct time zones in the world (one for each of the regular 24 hours, plus several others either with offsets that include an additional half hour or that follow daylight savings time differently than neighboring zones). So, any interface control that presents this information all at once is going to seem complex, long, or cluttered. This list is, in fact, almost 70% longer than the list of 30 basic zones, presenting 50 alternatives.

_Having the pull-down list is probably an attempt to prevent errors, because the user has to pick from this list. However, see the following discussion of this design decision and the related UARs that result from this analysis._

#### **Severity of the problem:  //问题的严重性**

Searching through this long list will always be time consuming for all users; the font is small and the list long. Admittedly, this is not a frequent task, so the cost is probably not too great.

_However, see the following discussion of this design decision and the related UARs that result from this analysis._

#### **Solution:  //解决方案**

At a minimum, analyze why there are 50 entries in this list and try to cut the number down.

However, I think it would be better to rethink using a pull-down ListBox for this task.

#### **Relationship to other UARs:  //与其他UAR的关系**

None when this UAR was originally written.

_But, there will be many before we finish this module!_

如果与其他UAR有关系，比如HE编号为4的问题，可以这样写。多个关系的话继续写就行了

UAR# HE4 The time zone the computer is set to is not displayed anywhere.

&nbsp;

&nbsp;

</div>