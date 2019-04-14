### Vue Router 源码学习笔记1 - 主要文件概览

#### 源码阅读思路

考虑到日常工作中，已经很熟悉 Vue Router 在 Vue 项目中的应用了，因此未以源码示例为入口，而是选择直接从目录入手的方式，index.js 就是起点。  


#### 主要文件功能及关系概览
从 index.js 入手，梳理功能，整理出目录中主要文件（除 utils 外）的功能以及它们之间的关联。得到了下面的这张图：  
![image](https://github.com/PennyOrAmy/LearningReport/blob/master/images/vur-router-files.png)

#### 下一步
对比三种不同的 history 的实现方式，即 HashHistory、HTML5History 和 AbstractHistory。
