title: 【JDK 8 源码】Lambada 表达式
date: 2019-10-15 05:35:00
categories: Java
tags: []
---
#Lambada 表达式

## JDK 8中引入了Lambada表达式

### 使用方法

```java
() -> code;//无参数
(params1,params2...) -> {body};//有参数 
a -> code;//一个参数
```
### Example
```java
package com.wangx.jdk8;

import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class SwingTest {
       public static void main(String[] args) {
       JFrame jframe = new JFrame("my frame");
       JButton jButton = new JButton("my jbutton");
           /**
 			匿名内部类
			**/
	//        jButton.addActionListener(new ActionListener() {
	//            @Override
	//            public void actionPerformed(ActionEvent e) {
	//                System.out.println("xxxxxxx");
	//            }
	//        });
			jButton.addActionListener(e -> System.out.println("Button press"));

			jframe.add(jButton);
			jframe.pack();
			jframe.setVisible(true);
			jframe.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);


		}
	}
```
![46471c115b456ad0b6dec878696fc12d.png](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2019/10/2597564428.png)