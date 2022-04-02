---
title: 使用 Next.js 和 Markdown 生成的静态博客
date: 2018-08-01 10:00:00
tags: Es6
---

<meta name="referrer" content="no-referrer"/>

## 一、 准备工作

**`1.1 创建项目`** 
我们建议使用 `create-next-app创建新的 [Next.js](https://www.nextjs.cn/docs/getting-started) 应用程序，它会自动为你设置所有内容。
在对应的项目文件夹中创建项目，终端执行命令
```
$ npx create-next-app .
```
❗️扩展：如果执行命令之后没反应，可以试试切换成淘宝镜像源。
```
$ npm config set registry https://registry.npm.taobao.org
```
**`1.2 安装依赖`** 
```
$ npm i marked gray-matter
```
**`1.3 运行项目`**
```
$ npm run dev
``` 
浏览器访问[http://localhost:3000](http://localhost:3000),可以看到项目正常启动！

## 二、 编写博客header

**`2.1 清理文件夹`**

* 删除`pages/api`文件夹
* 清理`pages/index.js`文件如下
```
import Head from 'next/head'

export default function Home() {
  return (
    <div>
      <Head>
        <title>Echo blog</title>
      </Head>
      <h2>Hello</h2>
    </div>
  )
}
```
* 删除`styles/Home.module.css`文件
* 更新`styles/globals.css`文件如下
```
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Poppins', sans-serif;
}

p {
  margin: 15px 0;
  line-height: 1.8;
}

a {
  text-decoration: none;
  color: #333;
}

img {
  width: 100%;
  border-radius: 10px;
}

header {
  background: steelblue;
  color: #fff;
  padding: 5px;
  margin-bottom: 40px;
}

header a {
  color: #fff;
}

.container {
  max-width: 768px;
  margin: auto;
  overflow: auto;
  padding: 0 10px;
}

.btn {
  display: inline-block;
  background: steelblue;
  color: #fff;
  border: none;
  padding: 8px 16px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}

.btn:focus {
  outline: none;
}

.btn:hover {
  transform: scale(0.98);
}

.btn-back {
  background: #f4f4f4;
  color: #000;
  margin-bottom: 20px;
}

.posts {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 30px;
  margin-top: 30px;
}

.card {
  padding: 15px;
  border-radius: 10px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
}

.card-page {
  padding: 15px 30px;
}

.post-title {
  margin: 10px 0;
}

.post-date {
  background: #f4f4f4;
  margin-bottom: 20px;
  padding: 3px 10px;
}

.post-body ul,
ol {
  font-size: 110%;
  line-height: 2.3;
  font-weight: bold;
  margin: 10px 0;
}

.post-body pre {
  background: #f4f4f4;
  padding: 20px;
  margin: 20px 0;
  line-height: 2.3;
}

@media (max-width: 500px) {
  .posts {
    grid-template-columns: 1fr;
  }
}
```
**`2.2 创建组件-添加header`**
* 根目录新建`components/Header.js`文件
```
import Link from 'next/link'
export default function Header() {
    return (
        <header>
           <div className="container"></div>
           <Link href="/">
               <h2>Echo blog </h2>
           </Link>

        </header>
    )
}

```
* 修改``如下
```
import Header from '../components/Header'
import '../styles/globals.css'

function MyApp({ Component, pageProps }) {
  return (
    <>
    <Header/>
    <div className="container">
      <Component {...pageProps} />
    </div>
    
    </>
  )
}

export default MyApp


```
浏览器可正常显示`header`
![image.png](https://upload-images.jianshu.io/upload_images/11846892-db37b5fbed2409b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 三、配置.md文件
**`3.1 添加md文件`**
* 根目录新建`posts/test.md`文件
```
---
title: '使用 Next.js 和 Markdown 生成的静态博客'
date: 'June 24 2021'
excerpt: 'This is a excerpt'
cover_image: '/images/posts/img1.jpg'
---
## Heading Two

* Item 1
* Item 2
* Item 3
```

---

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

