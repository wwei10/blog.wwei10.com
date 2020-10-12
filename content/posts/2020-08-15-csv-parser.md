---
title: "如何快速处理CSV文件"
date: 2020-08-15 17:16:00
slug: how-to-processing-csv-efficiently
---

最近领导碰到了个有趣的问题：处理csv文件。原来接触csv文件的时候都是直接用`split(',')`就搞定了，这次的问题稍稍复杂了些。

首先是，逗号可以存在于引号之间，其次是引号里面也可以存在引号，前提是两个引号一块儿出现。最关键的是这个csv文件还非常大（大约250M），每行有很多column。


<!--more-->

例子：`1999,Chevy,"Venture ""Extended Edition, Very Large""",,5000.00`。第一项1999，第二项Chevy，第三项Venture "Extended Edition, Very Large"，第四项空，第五项5000.00。

## 思路0

问领导能不能用第三方库？答曰不能。所以就不考虑了。

## 思路1

第一个思路是如果正常split不行，那能不能用正则表达式split？stack overflow上还真有一些解答。

```java
String regex = ",(?=(?:[^\\\"]*\\\"[^\\\"]*\\\")*[^\\\"]*$)";
String input = "1999,Chevy,\"Venture \"\"Extended Edition, Very Large\"\"\",,5000.00";
input.split(regex, -1);
```

这个[答案](https://stackoverflow.com/questions/1757065/java-splitting-a-comma-separated-string-but-ignoring-commas-in-quotes)挺有趣的，用到了正则表达式look ahead的概念。主要思想是寻找符合一下条件的逗号：逗号和句尾之间的引号数量为偶数。举个例子，`"1,2,\"3,\"`里面符合上述条件的只有1和2后面的逗号。

这个算法的缺陷是运行起来非常慢，处理一个大的csv大约需要3分钟。慢的原因是匹配每个逗号的时候都要从逗号位置向后数引号的数量，如果没有任何优化，算法复杂度`O(n^2)`。

## 思路2

第二个思路就是实现以上算法，但不用正则表达式。从后往前扫一遍，数引号的数量然后遇到逗号的时候，把现在的逗号到上一个符合条件的逗号之间的字符都加到结果里。

```java
public static ArrayList<String> parseLineV2(String line) {
   ArrayList<String> ret = new ArrayList<>();
   int numQuotes = 0;
   char[] arr = line.toCharArray();
   int lastCommaIndex = arr.length;
   for (int i = arr.length - 1; i >= 0; i--) {
       if (arr[i] == '"') {
           numQuotes += 1;
       } else if (arr[i] == ',' && numQuotes % 2 == 0) {
           // Add i to lastCommaIndex - 1 into ret.
           ret.add(0, line.substring(i + 1, lastCommaIndex));
           lastCommaIndex = i;
       }
   }
}
```

## 思路3

第三个思路是不去匹配符合规则的逗号，而是直接去匹配符合规则的csv的每一列。这个方法的缺点就是需要好好画画状态机，挺容易出错的。（mermaid-js用来画图很方便！）

![状态机](/images/state-machine.png)

代码的话相对简单，switch case加if else就好了。

```java
    public static ArrayList<String> parseLine(String line) {
        ArrayList<String> ret = new ArrayList<>();
        int start = 0;
        int state = 0;
        char[] charArray = line.toCharArray();
        for (int i = 0; i < charArray.length; i++) {
            char c = charArray[i];
            switch (state) {
                case 0:
                    if (c == ',') {
                        ret.add("");
                        start = i + 1;
                        state = 0;
                    } else if (c == '\"') {
                        state = 2;
                    } else {
                        state = 1;
                    }
                    break;
                case 1:
                    if (c == ',') {
                        ret.add(line.substring(start, i));
                        start = i + 1;
                        state = 0;
                    }
                    break;
                case 2:
                    if (c == '"') {
                        state = 3;
                    }
                    break;
                case 3:
                    if (c == ',') {
                        ret.add(line.substring(start, i));
                        start = i + 1;
                        state = 0;
                    } else if (c == '"') {
                        state = 2;
                    }
                    break;
                default:
                    System.out.println("shouldn't reach here");
                    break;
            }
        }
        if (charArray.length != start) {
            ret.add(line.substring(start, charArray.length));
        }
        return ret;
    }
```

## 总结

我造了一个比较长的字符串，运行1000000次看哪种算法最快。

```java
 String input = "1996,Jeep,Grand Cherokee,\"MUST SELL!\nair, moon roof, loaded\",4799.00,1,2,3,,4,,123,,5,\"hello, world\",\"hello, world\",6,,\"hello, world\",,8,,\"hello, world\",,";
```

1: 9秒
2: 0.85秒
3: 1秒

第二种和第三种速度差不多，写起来的话第二种容易想一些。

**最后的最后，如果能用csv parser库的话，其实直接用库比较好。**

-------------

## 附录：mermaid-js作图

```
stateDiagram-v2
    [*] --> 状态0 : 字符
    状态0 --> 状态0 : 字符
    [*] --> [*] : 逗号
    状态0 --> [*] : 逗号
    [*] --> 状态1 : 引号
    状态1 --> 状态1 : 字符
    状态1 --> 状态2 : 引号
    状态2 --> 状态1 : 引号
    状态2 --> [*] : 逗号
```