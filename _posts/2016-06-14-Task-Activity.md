---
layout: post
title: Task与Activity详解
tags: Android
---

### 1. Task是包含一系列Activity的堆栈, 遵循先进后出原则

### 2. Task默认行为: 
    (1)前提: Activity A和Activity B在同一个应用中
       操作: Activity A启动开僻Task堆栈(堆栈状态: A), 在Activity A中启动Activity B(堆栈状态: AB), 按下BACK返回键(堆栈状态: A). 
   
    (2)前提: Activity A和Activity B在同一个应用中, 应用名称为"TaskOne应用".
       操作: 在Launcher中单击"TaskOne应用"图标, Activity A启动开僻Task堆栈, 命名为TaskA(TaskA堆栈状态: A), 
       在Activity A中启动Activity B(TaskA堆栈状态: AB), 长按Home键, 返回Launcher, 启动其它应用(如:电子书), 
       开僻一个新Task堆栈, 命名: TaskB, 长按Home健, 返回Launcher, 单击"TaskOne应用"图标, 此时TaskA堆栈返回前台, 
       Activity B为栈顶应用, 供用户使用. 
   
    (3)前提: Activity A在名称为"TaskOne应用"的应用中, Activity C在名称为"TaskTwo应用"的应用中.
       操作: 在Launcher中单击"TaskOne应用"图标, Activity A启动开僻Task堆栈, 命名为TaskA(TaskA堆栈状态: A), 
			 在Activity A中启动Activity C(TaskA堆栈状态: AC),长按Home键, 返回Launcher, 启动"TaskTwo应用"即Activity C, 
			 开僻新的Task堆栈, 命名为TaskB, 按BACK键返回Launcher, 单击"TaskOne应用"图标, 此时TaskA堆栈返回前台, 
			 Activity B为栈顶应用, 供用户使用. 

### 3. Intent FLAG介绍:
    (1) FLAG_ACTIVITY_NEW_TASK: 

         a. 前提: Activity A和Activity B在同一个应用中. 

            操作: Activity A启动开僻Task堆栈(堆栈状态: A), 在Activity A中启动Activity B, 启动Activity B的Intent的Flag设为
                     FLAG_ACTIVITY_NEW_TASK, Activity B被压入Activity A所在堆栈(堆栈状态: AB).

            原因: 默认情况下同一个应用中的所有Activity拥有相同的关系(taskAffinity).

         b. 前提: Activity A在名称为"TaskOne应用"的应用中, Activity C和Activity D在名称为"TaskTwo应用"的应用中.
            操作1: 在Launcher中单击"TaskOne应用"图标, Activity A启动开僻Task堆栈, 命名为TaskA(TaskA堆栈状态: A), 
                   在Activity A中启动Activity C, 启动Activity C的Intent的Flag设为FLAG_ACTIVITY_NEW_TASK, 
                   Android系统会为Activity C开僻一个新的Task, 命名为TaskB(TaskB堆栈状态: C), 长按Home键, 选择TaskA, 
                   Activity A回到前台, 再次启动Activity C, 这时TaskB回到前台, Activity C显示, 供用户使用, 即: 
                   包含FLAG_ACTIVITY_NEW_TASK的Intent启动Activity的Task正在运行, 则不会为该Activity创建新的Task, 
                   而是将原有的Task返回到前台显示.

            操作2: 在Launcher中单击"TaskOne应用"图标, Activity A启动开僻Task堆栈, 命名为TaskA(TaskA堆栈状态: A), 
                   在Activity A中启动Activity C,启动Activity C的Intent的Flag设为FLAG_ACTIVITY_NEW_TASK, 
                   Android系统会为Activity C开僻一个新的Task, 命名为TaskB(TaskB堆栈状态: C),  在Activity C中启动
                   Activity D(TaskB的状态: CD) 长按Home键, 选择TaskA, Activity A回到前台, 再次启动Activity C, 
                   这时TaskB回到前台, Activity D显示,供用户使用.

    (2) FLAG_ACTIVITY_CLEAR_TOP:

        前提: Activity A, Activity B, Activity C和Activity D在同一个应用中.

        操作: Activity A启动开僻Task堆栈(堆栈状态: A), 在Activity A中启动Activity B(堆栈状态: AB), 在Activity B中启动
              Activity C(堆栈状态: ABC), 在Activity C中启动Activity D(堆栈状态: ABCD), 在Activity D中启动Activity B, 
              启动Activity B的Intent的Flag设置为FLAG_ACTIVITY_CLEAR_TOP, (堆栈状态: AB).

    (3) FLAG_ACTIVITY_BROUGHT_TO_FRONT:

         前提: Activity A在名称为"TaskOne应用"的应用中, Activity C和Activity D在名称为"TaskTwo应用"的应用中.

         操作: 在Launcher中单击"TaskOne应用"图标, Activity A启动开僻Task堆栈, 命名为TaskA(TaskA堆栈状态: A), 
               在Activity A中启动Activity C,启动Activity C的Intent的Flag设为FLAG_ACTIVITY_NEW_TASK,
               Android系统会为Activity C开僻一个新的Task, 命名为TaskB(TaskB堆栈状态: C), 在Activity C中启动
               Activity D(TaskB的堆栈状态: CD), 长按Home键, 选择TaskA, Activity A回到前台, 在Activity A中再次启动Activity C,
               在启动Activity C的Intent中设置Flag为FLAG_ACTIVITY_BROUGHT_TO_FRONT, TaskB回到前台, 
               Activity C显示, (TaskB的堆栈状态: C).

    (4) FLAG_ACTIVITY_MULTIPLE_TASK:
         与FLAG_ACTIVITY_NEW_TASK结合使用, 首先在Intent中设置FLAG_ACTIVITY_NEW_TASK, 打开Activity, 
         则启动一个新Task, 接着在Intent中设置FLAG_ACTIVITY_MULTIPLE_TASK, 再次打开同一个Activity,则还会新启动一个Task.

    (5) FLAG_ACTIVITY_SINGLE_TOP:
         当前Task堆栈中存在ABCD四个Activity, A是栈顶Activity, D为栈底Activity, 存在打开A的Intent中设置了
         FLAG_ACTIVITY_SINGLE_TOP标志, 则会使用栈顶A, 而不会从新New A.

    (6) FLAG_ACTIVITY_RESET_TASK_IF_NEEDED:
         例: 存在Activity A, Activity B, Activity C, Activity A启动开僻Task堆栈, 命名为TaskA(TaskA堆栈状态: A), 
              在Activity A中启动Activity B(TaskA堆栈状态: AB), 接着Activity B启动Activity C(TaskA堆栈状态: ABC), 
              启动Activity C的Intent中设置FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET标题, 这样TaskA中有一个还原点, 
              当有包含FLAG_ACTIVITY_RESET_TASK_IF_NEEDED的Intent请求TaskA堆栈时(比如请求Activity A)
              系统就会将还原点以上的Activity清除, TaskA堆栈中只剩下了AB.
 
### 4. launchMode介绍:
    (1) standard: 
         如果启动此Activity的Intent中没有设置FLAG_ACTIVITY_NEW_TASK标志, 则这个Activity与启动他的Activity在同一个Task中,
         如果设置了Activity请参考上面FLAG_ACTIVITY_NEW_TASK的詳細说明,"launchMode"设置为"standard"的
         Activity可以被实例化多次, 可以在Task中的任何位置, 对于一个新的Intent请求就会实例化一次.

   (2) singleTop: 
         如果启动此Activity的Intent中没有设置FLAG_ACTIVITY_NEW_TASK标志, 则这个Activity与启动他的Activity在同一个Task中, 
         如果设置了Activity请参考上面FLAG_ACTIVITY_NEW_TASK的詳細说明,"launchMode"设置为"singleTop"的Activity可以被
         实例化多次,  可以在Task中的任何位置, 对于一个新的Intent请求如果在Task栈顶, 则会用栈顶的Activity响影Intent请求, 
         而不会重新实例化对象接收请求, 如果没有在栈顶, 则会实例化一个新的对象接收Intent请求.

   (3) singleTask: 
       "launchMode"设置为"singleTask"的Activity总是在栈底, 只能被实例化一次, 它允许其它Activity压入"singleTask"的Activity
       所在的Task栈,如果有新的Intent请求有此标志的Activity, 则系统会清除有此标志的Task栈中的全部Activity,并把此Activity显示出来.

 

   (4) singleInstance: 
       launchMode"设置为"singleInstance"的Activity总是在栈底, 只能被实例化一次, 不允许其它的Activity压入"singleInstance"
       的Activity所在Task栈,  即整个Task栈中只能有这么一个Activity.

 

### 5. taskAffinity属性: 
   (1) taskAffinity属性应和FLAG_ACTIVITY_NEW_TASK标志及allowTaskReparenting属性结合使用, 如果只使用taskAffinity属性, 
       请参考上面Task默认的行为.

   (2) 与FLAG_ACTIVITY_NEW_TASK标志结合:
       a. 前题: Activity A和Activity B在同一个应用中, Activity A与Activity B设置不同的taskAffinity属性.
          操作: Activity A启动开僻Task堆栈,命名为TaskA(TaskA堆栈状态: A), 在Activity A中启动Activity B, 启动Activity B
                   的Intent中设置FLAG_ACTIVITY_NEW_TASK标志,这时系统会新开僻一个Task堆栈,TaskB(TaskB堆栈状态: B).

       b. 前题: Activity A在"TaskOne应用"中, Activity C在"TaskTwo应用"中, Activity A和ActivityC设置了相同的taskAffinity属性.
          操作: Activity A启动开僻Task堆栈,命名为TaskA(TaskA堆栈状态: A), 在Activity A中启动Activity C, 启动Activity C的
                Intent中设置FLAG_ACTIVITY_NEW_TASK标志,这时Activity C会压入与Activity A堆栈相同的TaskA堆栈(TaskA堆栈状态: AC).

 

   (3) 与allowTaskReparenting属性:
         例: 在"TaskOne应用"中有一个天气预报Activity A, Activity A与"TaskOne应用"中的其它Activity有默认的关系
             (taskAffinity属性都没有设置), 并且allowTaskReparenting属性设置为true, 现在存在一个"TaskTwo应用
             "启动了"TaskOne应用"中的天气预报Activity A,  这时Activity A与"TaskTwo应用"中的Activity在同一个Task, 
             命名这个Task堆栈为TaskA, 这时"TaskOne应用"启动, 并且又打开发天气预报Activity A, 这时Activity A会从TaskA堆栈中转移到
             "TaskOne应用"所在的堆栈, 即Activity A可以在多个堆栈中来回转移.       
    
### 6. alwaysRetainTaskState属性: 
    如果Task堆栈中的Root Activity设置了此属性值为true, 不管出现任何情况, 一直会保留Task栈中Activity的状态.

### 7. clearTaskOnLaunch属性:     
    如果Task堆栈中的Root Activity设置了此属性值为true, 只要你一离开这个Task栈, 则系统会马上清理除了Root Activity的全部Activity. 

### 8. finishOnTaskLaunch属性: 
    如果某Activity设置了finishOnTaskLaunch属性, 只要你一离开这个Task栈, 则系统会马上清除这个Activity,
    不管这个Activity在堆栈的任何位置.
