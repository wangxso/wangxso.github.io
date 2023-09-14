title: Vue router param 无法传参
date: 2021-02-06 08:07:05
categories: zczb
tags: []
---
# Vue router param 无法传参

原因是
```js
this.$router.push({path: "/home",params:{...}})
```
改为
```js
this.$router.push({{path: "/home", name: "home", params:{...}}})
```
就行了
#### 使用params传参只能使用name进行引入