# Vim常用命令



h：帮助文档

j：下

k：上

h：左

l：右

f：往下翻页

b：往上翻页

d：下翻半页

u：上翻半页

g：跳转到第一行

G：跳转到最后一行

数字+j：移动指定行，比如`3j`往下移动3行；`5j`往下移动5行

自上而下搜索：/，比如自上而下搜索print这个单词，输入：/print，然后回车，如果有匹配的，就会高亮显示，n可以切换到下一个；切换到上一个是大写字母N

自下而上搜索：?，比如自下而上搜索print这个单词，输入：?print

## 设置高亮

1. 搜索关键词：/关键词
2. 命令模式下输入，:set hlsearch

## 替换

命令模式下

```shell
%s/旧值/新值/g
```

/g 表示替换一行中所有的