---
layout: post
title: vue父组件调用子组件
date: 2020-01-13 19:42:21
tags: ['vue','前端']
---

# vue父组件调用子组件

#### 起因

感觉被这个问题困扰了很久，在vue的项目中，通过router翻页后，codeMirror组件中的数据不会恢复初始化。

#### 沉沦

一度以为就要放弃了，在沉寂了好久之后，终于想到，在项目的codeMirror子组件中，有reset()方法，所以，如果能够在翻页时调用这个reset方法，就可以解决这个问题。

#### 转折

重新思考了这个问题，发现通过watch函数，可以实现监听页面路由的变化。因此，在路由变化时完成调用就可以。

一般子组件在父组件中被调用， 如果想要父组件调用子组件的方法，

（1）首先要通过ref 添加一个占位。

```javascript
  <Exam ref="test" v-for="(list,index) in examInfo"
          :title="list.title"
          :question="list.question"
          :index="-1"
          :id="list.id"
          :customBh="list.customBh"
          :isProgramBlank="list.isProgramBlank"
          :src="list.src"
          :code-history="list.codeHistory"
          :score="list.score">
    </Exam>
```



这里把Exam子组件声明为ref。

（2）调用 通过this.$refs.test.reset() 即可完成对子组件中方法的调用。、

this.$ref.test   ===>test是上述的ref中声明的占位名称。

.reset() 是Exam子组件中的方法。

```javascript
 resetExamCode(){
          this.$nextTick(()=>{
            this.$refs.test[0].reset()
            console.log("it works")
          })
          },
```

#### 失之毫厘，差之千里

本以为这样的调用就可以完成任务，但是还是想的太简单了。

（1）子组件中必须用<div></div>标签包裹。

*这一点，很多文章中 都有提到*

（2）如果父组件页面很复杂，需要考虑子组件方法的调用时机，在某些情况下，甚至会出现因为未渲染完成而导致子组件方法调用失败。

通过查询资料，发现vue中提供了对应的判断方法。

this.$nextTick()=>{} 方法，是vue回调函数，可以在页面加载完成后执行{}中的方法。

（3）以为有这两个就够了吗？nonono。因为项目中使用了v-for循环，所以，Exam组件其实是个组件数组。（虽然数组中只有一个元素）。但是还是要声明：this.$refs.test[0].xxxxx.





#### 反思

如果一个问题真的很难解决，那很大概率就是方法错了，不如重新来过，重新思考，一定能更好的解决。//除了数学（^-^）