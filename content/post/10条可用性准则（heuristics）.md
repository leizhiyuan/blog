---
title: 10条可用性准则（Heuristics）
tags:
  - 大学
  - 学习
  - 笔记
  - 编程
id: 764
categories:
  - 学习笔记
date: 2012-05-29 06:52:48
---

SSD4第二单元其实就讲了这么一点东西，包括一点VB的控件常识

可用性测试（Usability testing），是一项通过用户的使用来评估产品的技术，由于它反应了用户的真实使用经验，所以可以视为一种不可或缺的可用性检验过程[1]。也就是说，可用性测试是指让用户使用产品（服务）的设计原型或者成品，通过观察，记录和分析用户的行为和感受，以改善产品（服务）可用性的一系列方法。它适用于产品（服务）前期设计开发，中期改进和后期维护完善的各个阶段，是用户中心设计的思想的重要体现。

10条可用性准则（Heuristics）
These are ten general principles for user interface design. They are called "heuristics" because they are more in the nature of rules of thumb than specific usability guidelines.

1.Visibility of system status——系统状态的可见性

The system should always keep users informed about what is going on, through appropriate feedback within reasonable time.

系统应该始终在合理的时间以适当的反馈信息让用户知道系统正在做什么。

2.Match between system and the real world——系统和现实世界之间的吻合

The system should speak the users' language, with words, phrases and concepts familiar to the user, rather than system-oriented terms. Follow real-world conventions, making information appear in a natural and logical order.

系统应该用用户熟悉的词，短语和概念来说用户的语言，而不是用面向系统的术语。遵循现实世界中的惯例，让信息以自然的合乎逻辑的次序展现在用户面前。

3.User control and freedom——用户控制和自由

Users often choose system functions by mistake and will need a clearly marked "emergency exit" to leave the unwanted state without having to go through an extended dialogue. Support undo and redo.

用户经常错误地选择系统功能，所以在不需要查看由于误操作而延伸出来地对话的情况下有一个明显地标志为“紧急退出”的操作来离开不想要的状态。另外，系统需要支持“撤销操作”和“重做”的功能。

4.Consistency and standards——一致性和标准

Users should not have to wonder whether different words, situations, or actions mean the same thing. Follow platform conventions.

用户不必去担心是否不同的词，情形或动作意味着同一件事情。 遵循平台惯例。

5.Error prevention——预防错误的发生

Even better than good error messages is a careful design which prevents a problem from occurring in the first place. Either eliminate error-prone conditions or check for them and present users with a confirmation option before they commit to the action.

一个事先就能预防问题发生的细致的设计要比好的错误提示信息好的多。要么消除容易出错的条件，要么检查它们，然后在用户犯错之前给用户一个明确的选项。

6.Recognition rather than recall——识别而不是回忆

Minimize the user's memory load by making objects, actions, and options visible. The user should not have to remember information from one part of the dialogue to another. Instructions for use of the system should be visible or easily retrievable whenever appropriate.

使每个对象，动作，和选项都是可见的。用户在从对话的一部分到另一部分的过程不必去记忆信息。系统使用说明在任何适当的时候都应该使可见的或者很容易被获取。

7.Flexibility and efficiency of use——使用的舒适性和高效性

Accelerators -- unseen by the novice user -- may often speed up the interaction for the expert user such that the system can cater to both inexperienced and experienced users. Allow users to tailor frequent actions.

加速器－初级用户未见过的－可以经常加快专家用户的交互操作，如此系统就能同时迎合无经验和经验丰富的用户的需要。允许用户进行频繁的操作。

8.Aesthetic and minimalist design——有审美感的和内容最少的设计

Dialogues should not contain information which is irrelevant or rarely needed. Every extra unit of information in a dialogue competes with the relevant units of information and diminishes their relative visibility.

对话中不应该包含无关的或者很少需要的信息。任何一个对话中的额外信息会严重影响对话中相关的信息并降低这些相关信息的可见性。

9.Help users recognize, diagnose, and recover from errors——帮助用户识别错误，诊断错误并从错误中恢复过来

Error messages should be expressed in plain language (no codes), precisely indicate the problem, and constructively suggest a solution.

错误提示信息应该用简单的语言而非代码来表达，正确恰当地指出问题所在，并建设性地提供一个解决办法。

10.Help and documentation——帮助和说明

Even though it is better if the system can be used without documentation, it may be necessary to provide help and documentation. Any such information should be easy to search, focused on the user's task, list concrete steps to be carried out, and not be too large.

虽然系统最好是可以让用户不需要任何说明就能使用，但是提供一个帮助和说明的功能还是有必要的。注重与用户的任务，任何这样的信息都要很容易的就可以被用户找到，列出明确的步骤去实现这些操作，而且篇幅不要太长。