--
layout:     post                            # 使用的布局（不需要改）
title:      Android Activity的四种加载模式             # 标题
subtitle:      #副标题
date:       2016-11-28                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


## Activity有四种加载模式

### 1. standard: 标准(默认)模式
> 当通过这种模式来启动Activity时,Android总会为目标Activity创建一个新的实例,并将该Activity添加到当前Task栈中.注意,这种方式不会启动新的Task,只是将新的Activity添加到原有的Task 


### 2. singleTop: Task顶单例模式
> 该模式和standard模式基本一致,但有一点不同:当将要被启动的Activity已经位于Task栈顶时,系统不会重新创建目标Activity实例,而是直接复用Task栈顶的Activity 


### 3. singleTask: Task内单例模式
> 采用该加载模式时,Activity在同一个Task内只有一个实例. 
 当系统采用singleTask模式加载Activity时,又分为以下三种情况: 
 * (1)如果将要启动的Activity不存在,那么系统将会创建该 实例,并将其加入Task栈顶 
 * (2)如果将要启动的Activity已存在,且存在栈顶,那么此时与singleTop模式的行为相同 
 * (3)如果将要启动的Activity存在但是没有位于栈顶,那么此时系统会把位于该Activity上面的所有其他Activity全部移出Task,从而使得该目标Activity位于栈顶  


### 4. singleInstance: 全局单例模式 
> 在此种加载模式下,无论从哪个Task中启动目标Activity,只会创建一个目标Activity实例且会用一个全新的Task栈来装载该Activity实例. 
 当系统采用singleInstance模式加载Activity时,又分为以下两种情况: 
 * (1)如果将要启动的Activity不存在,那么系统将会先创建一个全新的Task,再创建目标Activity实例并将该Activity实例放入此全新的Task中 
 * (2)如果将要启动的Activity已存在,那么无论它位于哪个应用 程序,哪个Task中;系统都会把该Activity所在的Task转到前台,从而使该Activity显示出来 



## Android对于Activity的管理方式
> Android采用Task来管理多个Activity.
 * 当启动一个APP时,Android就会为之创建一个Task 然后启动这个应用的入口Activity. 
 * 但是Android并没有为Task提供API,开发者无法真正地访问Task,只能调用Activity的getTaskId()方法来获取其所在的Task的ID.事实上,我们可以把Task理解成 
 * Activity栈,Task以栈的形式来管理Activity:将不断启动的Activity压入栈.即:先启动的Activity被放入栈底, 后启动的Activity放在Task栈顶 


## 测试步骤及解释: 
1. 进入MainActivity,点击按钮进入AnotherActivity. 
2. 此时在AnotherActivity界面.目前在Task栈中有两个Activity.由下至上为:MainActivity-->AnotherActivity 
3. 在AnotherActivity界面点击按钮,系统以标准模式再次加载一个MainActivity 
4. 此时在MainActivity界面.目前在Task栈中有三个Activity. 
 由下至上为:MainActivity-->AnotherActivity-->MainActivity 
5. 此时在MainActivity界面,再点击按钮准备进入AnotherActivity系统将以singleTask模式再次加载AnotherActivity.正因为是以singleTask模式加载,所以会清除AnotherActivity上部的所有Activity. 
6. 此时在AnotherActivity界面.目前在Task栈中有两个Activity.由下至上为:MainActivity-->AnotherActivity 
 
 这就和第二步的状态一致了