---
title: Vue 的双向绑定原理
date: 2023-04-20 11:12:22
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>


# 4、

**定义：** vue中双向绑定指的是一个指令v-model，可以绑定一个响应式数据到视图，同时视图变化能改变该值

**好处：** v-model是语法糖，默认相当于:value和@input，使用v-model可以减少大量繁琐的事件处理代码，提高开发效率

**使用场景：** 通常在表单项上使用，也可以在自定义组件上使用，表示对某个值的输入和输出的控制。 可以结合lazy、number、trim对v-model的行为做进一步限定
    
    lazy：在输入框中，v-model默认是在input事件中同步输入框的数据(除了提示中介绍的中文输入 法情况外)，使用修饰符.lazy会转变为在change事件中同步
    number：将value转换为number类型
    trim：去除value左右两侧的空格

**原理：** 编译器根据表单元素的不同会展开不同的DOM属性和事件对，比如text类型的input和textarea会展开为value和input事件；checkbox和radio类型的input会展开为checked和change事件；select用value作为属性，用change作为事件

**vue2和vue3中区别：** 在vue2中使用v-bind添加sync修饰符:value.sync，实现和v-model相同的作用，而vue3中废除了sync修饰符，vue3中的v-model默认相当于modelValue和@update:modelValue,vue3中甚至可以用参数形式指定多个不同的绑定，如v-model:foo和v-model:bar
