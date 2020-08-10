---
title: JS数据结构与算法
date: 2020-08-01 20:40:15
categories: JS
top: false
summary: JS数据结构与算法
tags: 
 - JS
---

## JS数据结构与算法

- 数据结构与算法是什么？

  - 数据结构：计算机存储、组织数据的方式，就像**锅碗瓢盆**
  - 算法：一系列解决问题的清晰指令，就像**食谱**

- 数据结构与算法的关系

  - 程序 = 数据结构 + 算法
  - 数据结构为算法提供服务，算法围绕数据结构操作

- 将要学习的数据结构

  - 栈、队列、链表
  - 集合、字段
  - 树、堆、图

- 将要学习的算法

  - 链表：遍历链表、删除链表节点
  - 树、图：深度/广度优先遍历
  - 数组：冒泡/选择/插入/归并/快速排序、顺序/二分收索

- 时间复杂度是什么？

  - 一个函数，用大O表示，比如O(1),O(n),O(logN)....，主要记住：  O(n^2) > O(n) > O(log_2 N) > O(1)
  - 意义：定性描述改算法的运行时间(只是描述一个趋势)

  ![时间复杂度](C:/Users/user/Desktop/medias/imges/js/sf/sf1.png)

  - 实例:

    - O(1)[代码永远只会执行一次]

    ```js
    let i = 0;
    i+1 = 1
    ```

    - O(n) 代码会被执行N次

    ```js
    for(let i=0;i<n;i++) {
        console.log(i)
    }
    ```

    - O(1) + O(n) = O(n)，两个时间复杂度相加取最大的

    ```js
    let i = 0;
    i+1 = 1;
    for(let j=0;j<n;j++) {
        console.log(j)
    }
    ```

    - O(n) * O(n) = O(n^2)，两个时间复杂度相乘算法与相加不一样

    ```js
    for(let i=0;i<n;i++) {
        for(let j =0;j<n;j++) {
            console.log(i,j)
        }
    }
    ```

    - O(logN)

    ```js
    let i=1;
    while(i<n) {
        console.log(i)
        i * =2 ;
    }	
    ```

- 空间复杂度是什么？

  - 一个函数，用大O表示，比如O(1),O(n),O(n^2)....，与时间一样

  - 意义：算法在运行过程中占用存储空间大小的量度，越小越好

  - 实例：

    * O(1),这段代码占用一个内存存储单元

    ```js
    let i=0
    i+ = 1
    ```

    * O(n)

    ```js
    const list = []
    for(let i=0;i<n ;i+=1 ) {
        list.push(i)
    }
    ```

    * O(n^2)

    ```js
    const matrix = []
    for(let i=0;i<n;i++) {
        matrix.push([])
        for(let j=0;j<n;j++) {
            matrix[i].push(j)
        }
    }
    ```

## 一、栈

* 概念：
  * 一个**后进先出**的数据结构
  * JS中没栈，但可以用Array实现栈的所有功能

![栈](/medias/imges/js/sf/sf2.png)

* 栈的应用场景

  * 需要**后进后出**的场景

    比如：十进制转二进制、判断字符串的口号是否有效、函数调用堆栈....

    * 十进制转二进制，后出来的余数反而要排到前面，把余数依次入栈，然后再出栈，就可以实现余数倒序输出
    * 有效括号：越靠后的左括号，对应的右括号越靠前。左括号入栈，右括号出栈，最后栈空了就是合法的。
    * 函数调用堆栈：最后调用的函数，最新执行完。JS解释器使用栈来控制函数的调用顺序

![用栈的场景](/medias/imges/js/sf/sf3.png)

* 实例：

  1. leetCode的20题：括号问题

  >  给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。 有效字符串需满足：
  >
  >  1. 左括号必须用相同类型的右括号闭合。
  >  2. 左括号必须以正确的顺序闭合。
  >
  >  注意空字符串可被认为是有效字符串
  >
  >  输入: "()"  "()[]{}"
  >  输出: true true

  ```js
  /**
   * @param {string} s
   * @return {boolean}
   */
  var isValid = function(s) {
      if(s.length%2 ===1 ) {return false}
      const stack = []
      for(let i=0;i<s.length;i++) {
          const c = s[i]
          if(c==='(' || c==='{' || c==='[') {
              stack.push(c)
          }else {
              const t = stack[stack.length-1]
              if(
                  (t==='(' && c ===')') || 
                  (t==='{' && c==='}') ||
                  (t==='[' && c===']')  
              ) {
                  stack.pop()
              }else {
                  return false
              }
          }
      }
      return stack.length === 0
  };
  ```

  