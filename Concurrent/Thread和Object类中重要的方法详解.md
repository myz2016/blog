# Thread和Object类中重要的方法详解

## 方法概览

| 类     | 方法名                    | 简介                                                         |
| ------ | ------------------------- | ------------------------------------------------------------ |
| Thread | sleep相关                 | 本表格的“相关”，指的重载方法，也就是方法名相同，但是参数不同，例如sleep有多个方法，只是参数不同，实际作用大同小异 |
|        | join                      | 等待其他线程执行完毕                                         |
|        | yield相关                 | 放弃已经获取到的cpu资源                                      |
|        | currentThread             | 获取当前执行线程的引用                                       |
|        | start，run相关            | 启动线程相关                                                 |
|        | interrupt相关             | 中断线程                                                     |
|        | stop，suspend，resume相关 | 已废弃                                                       |
| Object | wait/notify/notifyAll相关 | 让线程暂时休息和唤醒                                         |

## wait/notify/notifyAll 方法详解

## sleep 方法详解

## join 方法详解