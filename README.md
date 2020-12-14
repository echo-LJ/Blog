## 提交代码执行步骤
1、提交到暂存区
```
$ git add . //(包括文件内容修改(modified)以及新文件(new))
// $ git add -u (他会将被修改的文件提交到暂存区)
// $ git add -a (包含所有文件)
```
2、输入提交命令
```
$ git cz
```
选择commit 的类别
```
    feat：新增功能（feature）
    fix：修补bug
    docs：仅仅修改了文档，比如 README, CHANGELOG, CONTRIBUTE等等
    style： 仅仅修改了空格、格式缩进、逗号等等，不改变代码逻辑
    refactor：重构（即不是新增功能，也不是修改bug的代码变动）
    test：增加测试，包括单元测试、集成测试等
    chore：构建过程或辅助工具的变动
    type：代表某次提交的类型，比如是修复一个bug还是增加一个新的feature。
    perf: 优化相关，比如提升性能、体验
    revert: 回滚到上一个版本
    ci：自动化流程配置修改
```
输入命令后依次提示：
```
1. 上、下键选择要提交的更改类型

2. 此更改的范围是什么(例如组件或文件名)?(按回车键跳过)

3. 写一个简短的祈使句来描述这个变化

4. 提供更详细的更改说明:(按回车键跳过)

5. 有什么重大变化吗?

6. 这一变化是否会影响
任何未解决的问题?
```
3、推送到远程
```
$ git push origin <-分支名->
```
## 运行项目
```
hexo g
hexo server
```
在线访问项目地址：http://localhost:4000/.

## 部署项目
首先将项目中有关文件提交！
```
删除.deploy_git
$ hexo clean 
$ hexo g
$ hexo d
```
线上访问项目地址： https://lujinggirl.github.io/

## 生成博客文章的一些常用命令
```
hexo new "postName" #新建文章  
hexo new page "pageName" #新建页面  
hexo generate #生成静态页面至public目录  
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）  
hexo deploy #将.deploy目录部署到GitHub  
hexo help  #查看帮助  
hexo version  #查看Hexo的版本
```
## 博客文章引用网络图片
* 文章头部添加
```
<meta name="referrer" content="no-referrer"/>
```