---
title: Monorepo 是什么？你可能需要了解一下
date: 2022-08-03 10:00:00
tags: Monorepo
---

<meta name="referrer" content="no-referrer"/>

![截屏2022-08-04 上午10.02.26.png](https://upload-images.jianshu.io/upload_images/11846892-e185b5a8fb094607.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 概览


![monorepo.png](https://upload-images.jianshu.io/upload_images/11846892-a7fe4718519a3af7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Monorepo (单体仓库) 是一种代码的管理方式，这种方式可以加速你的开发工作流，本文将讲述 Monorepo 的优缺点，它和 Multirepo (多体仓库：Multiple repositories ` [ˈmʌltəpəl] [rɪˈpɑzɪˌtɔri, -ˌtori]`) 的区别，如何使用工具提高开发效率，以及判断你的开发团队是否需要切换到 Monorepo。

### 什么是 Monorepo？

Monorepo 中文：单体仓库 (单个仓库)。就是单个项目仓库 (repository)，其中包含多个开发项目 project (模块 module，或包 package)。虽然这些 project 也许是相关联的，但它们通常在逻辑上相互独立，并被不同的团队负责编写，运行。

有些公司组织他们的代码在单个仓库里，并共享给每一个人。Monorepos 能容纳非常大的代码量，举个例如 Google 也许每天都会产出成百上千的 commits 大小超过 80TB。其他知名公司也在以 monorepo 方式管理自己的代码，如微软、Facebook、Twitter。

Monorepos 有时也被称作 Monolithic repositories `[ˌmɒnə'lɪθɪk] [rɪˈpɑzɪˌtɔri, -ˌtori]`，但它不能和 Monolithic architecture (单体架构) 混淆。单体架构是一种用于编写自包含应用程序 (Self-contained applications) 的软件开发实践。一个例子就是 Ruby on Rails monolith 能够处理许多个网站，API Endpoints 以及 Background Jobs.

### Monorepos 和 Multirepos 的对比

与 Monorepo 相反的是 Multirepo (多体仓库：多个 project 保存在互相完全独立的代码仓库内)。Multirepo 来的很自然 — 也就大多数人都知道如何创建一个新的 project。毕竟，谁不喜欢一个干净的崭新的开始呢？

从 multi 迁移到 monorepo 就是将所有 project 移到单个代码仓库里。



![02-multi-to-mono-1056x934-1.jpeg](https://upload-images.jianshu.io/upload_images/11846892-365731ba5359e10f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当然，这只是开始。当我们进入重构和整合阶段时，艰难的工作就来了。
```
$ mkdir monorepo
$ git init
$ mv ~/src/app-android10 ~/src/app-android11 ~/src/app-ios .
$ git add -A
$ git commit -m "My first monorepo"
```

multirepo 不是 microservices (微服务) 的同义词，两者互不相干。实际上，稍后我们会讨论结合 monorepo 和 microservices 两种模式的公司，monorepo 可以管理任意数量的 microservice，只要仔细的设置用于部署的 Continuous Integration and Delivery (CI/CD) pipeline 就行。

### Monorepos 的优点

乍眼一看，选择 Monorepos 和 Multirepos 似乎没什么大不了的，但这一步选择将深远的影响你公司的开发工作流。关于它们的好处，我们可以列出一些：

* `透明度`：所有人都能看到所有人写的代码，这一特点能够更好的协作和跨团队贡献 (collaboration and cross-team contributions)，另一个团队中的开发人员可以修复代码中你甚至不知道存在的 bug。
* `简化依赖管理`：共享依赖是轻而易举的。几乎不需要用到包管理器，因为所有 module 都保存在在同一个代码仓库中。
* `Single 的真正原因`：所有依赖仅有一个版本，意味着没有版本冲突和 Dependency hell (相依性地狱、依赖地狱)。
* `一致性`：当你将所有代码库集中在一个地方时，执行代码质量标准和统一风格会更容易。
* `共享时间线`：当 API 或共享的库中有重大变更 (breaking changes) 时就会立刻暴露出来，迫使不同的团队提前沟通并联合起来。每个人都致力于跟上变化。
* `Atomic Commits`：Atomic Commits 使大规模重构更容易。开发人员可以在一次 commit 中更新多个包或项目。
* `Implicit CI`: 由于所有代码已经统一在一个地方，因此保证了[持续集成](https://semaphoreci.com/continuous-integration).
* `Unified CI/CD`: 您可以为 repo 中的每个项目使用相同的 [CI/CD](https://semaphoreci.com/cicd)部署过程.
* `Unified build process`: 我们可以为 仓库 中的每个应用程序使用共享的 [构建过程](https://semaphoreci.com/continuous-integration).

### Monorepos 的缺点

随着 monorepos 越来越庞大，我们在版本控制工具、构建系统和 CI pipelines(集成管道) 方面会面临设计极限。这些问题会让公司决定重新用回 multirepo。

* `糟糕的性能`：monorepos 很难扩大规模。诸如 git blame 之类的命令可能会花费非常长的时间，IDE 开始卡顿，生产力受到影响，而且在每次提交后的测试阶段变得苦不堪言。
* `污染主线分支 main/master`：一个烂的 master branch 会影响所有在 monorepo 工作的人。这可被视为导致灾难，或者是保持测试 tests clean 和 up to date 的诱因。
* `学习曲线`：如果项目仓库中有跨越许多紧密耦合的 project，那么新开发人员的学习曲线将会更加陡峭。
* `大量数据`：monorepos 每天会处理大量的数据和 commit。
* `文件权限管理`：管理文件权限更具挑战性，因为像 Git 或 Mercurial 这样的系统没有内置的权限管理机制。
* `代码审查`：通知 (notifications) 会变得非常嘈杂。例如，GitHub 的通知功能，当一连串的 pull requests 和 code reviews 时并不友好。

你可能已经注意到，这些问题大多是技术性的 (technical)。在以下部分中，我们将了解坚持 monorepos 的公司如何通过投入精力到工具 (tooling)、添加集成 (integrations) 和 编写自定义解决方案 (writing custom solutions) 来解决大部分问题。

### Monorepo 的历史

Monorepos 已经存在很长时间了。三十年来，FreeBSD 一直使用 CVS 和后来的 subversion monorepos 进行开发和包的分发。

许多开源项目已经成功地使用了 monorepos。例如：

* [Laravel](https://laravel.com/)：一套 PHP Web 开发框架
* [Symfony](https://symfony.com/)：另一个用 PHP 编写的 MVC 框架。有趣的是，他们为每个 Symfony 工具和库创建了只读仓库。这种方法称为 split-repo (仓库分割？)。
* [NixOS](https://github.com/NixOS/nixpkgs/)：这个 Linux 发行版使用 monorepo 来发布 packages。
* [Babel](https://github.com/babel/babel/blob/master/doc/design/monorepo.md)：一种用于 Web 开发的流行 JavaScript 编译器。一个 monorepo 管理整个项目及其所有插件。
此外，[React](https://github.com/facebook/react)、 [PEmber](https://github.com/emberjs/ember.js/tree/master/packages) 和 [Meteor](https://github.com/meteor/meteor/tree/devel/packages)  等前端框架都使用 monorepos。
然而，真正的问题是商业软件是否可以从 monorepo 布局中受益。考虑到优缺点，让我们听听一些公司的经验。

### Segment: 再见 multirepos
Alex Noonan 讲述了一个关于 [告别 multirepos](https://segment.com/blog/goodbye-microservices/) 的故事。他工作的公司 Segment.com 提供事件收集和转发服务。他的每个客户都需要以特殊格式使用数据。因此，工程团队最初决定混合使用 microservices 和 multirepos。

该策略运作良好 —— 随着客户群的增长，他们毫无问题地扩大规模。但是，当转发目标的数量超过一百个大关时，事情开始变得崩溃。维护、测试和部署 140+ 个仓库（每个仓库都有数百个日益分散的依赖项）的管理负担太重了。


“最终，该团队发现自己无法取得进展，三名全职工程师大部分时间都在保持系统运作。”

“Eventually, the team found themselves unable to make headway, with three full-time engineers spending most of their time just keeping the system alive.”

对于 Segment 而言，补救措施是合并 (consolidation)。该团队将所有服务和依赖项迁移到单个 monorepo 中。虽然迁移成功，但它非常繁重，因为他们每次都必须协调共享库并测试所有内容。尽管如此，最终结果还是降低了复杂性并提高了可维护性。

“证据是速度的提高。 […] 在过去的 6 个月中，我们对库的改进比 2016 年全年都要多。”

“The proof was in the improved velocity. […] We’ve made more improvements to our libraries in the past 6 months than in all of 2016.”

许多年后，当一个小组询问她在 microservices 方面的经验时，Alex 解释了转向 monorepo 的原因：

“结果并没有像我们想象的那样具有优势。我们改变它的主要动机是测试失败会影响不同的东西 [..] 将它们拆分成单独的 repos 只会让情况变得更糟，因为你去接触六个月都没有碰过的东西。这些测试完全被破坏了，因为你没有被迫花时间修复它。我见过的东西成功拆分为单独的 repo 而不是 services 的唯一情况之一是，当我们有一大块代码在多个 services 之间 shared 时，我们希望将其变成一个共享库。除此之外，我认为我们已经发现，即使我们已经转向 microservices，我们仍然更喜欢 mono repo 中的东西。”

“It didn’t turn out to be as much of an advantage as we thought it was going to be. Our primary motivation for breaking it out was that failing tests were impacting different things [..] Breaking them out into separate repos only made that worse because now you go in and touch something that hasn’t been touched in six months. Those tests are completely broken because you aren’t forced to spend time fixing that. One of the only cases where I’ve seen stuff successfully broken out into separate repos and not services is when we have a chunk of code that is shared among multiple services, and we want to make it a shared library. Other than that, I think we’ve found even when we’ve moved to microservices, we still prefer stuff in a mono repo.”


等等多个故事...


### 优秀的工具
如果我们必须从所有这些故事中吸取一个教训：适当的工具是有效 monorepos 的关键 —— 需要重新思考 building 和 testing。我们可以使用聪明的 building tool，只对自上次提交以来发生变化的部分采取行动，而不是在每次更新时重建一整个的 repo，这个构建系统能理解项目的结构，只在自上次提交以来发生变化的部分起作用。


![build-1056x258-1.jpeg](https://upload-images.jianshu.io/upload_images/11846892-cadb4da1387faa0b.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们大多数人都没有 Google 或 Facebook 的内部资源。我们可以做什么？幸运的是，许多大公司已经开源了他们的 build systems (构建系统)：

*  [Bazel](https://bazel.build/)：Google 发布的，部分基于他们自己开发的构建系统 (Blaze)。Bazel 支持多种语言，并且能够大规模构建和测试。
*  [Buck](https://buck.build/)：Facebook 的开源快速构建系统。支持多种语言和平台上的差异化构建。
*  [Pants](http://www.pantsbuild.org/)：Pants 构建系统是与 Twitter、Foursquare 和 Square 合作开发的。目前，它仅支持 Python，未来将支持更多语言。
*  [RushJS](https://rushjs.io/)：微软用于 JavaScript 的可扩展 monorepo 管理器，能够从单个仓库构建和部署多个 packages。

Monorepos 越来越受到关注，尤其是在 JavaScript 中，如以下项目所示：

* [Lerna](https://github.com/lerna/lerna)：JavaScript 的 monorepo 管理器。 与 React、Angular 或 Babel 等流行框架集成。
* [Yarn Workspaces](https://classic.yarnpkg.com/en/docs/workspaces/)：在一个仓库的根目录下安装和更新多个 Node.js 依赖。 
* [ultra-runner](https://github.com/folke/ultra-runner)：用于 JavaScript 的 monorepo 管理脚本。同时支持 Yarn、pnpm 和 Lerna。支持 parallel builds (并行构建)。
* [Monorepo builder](https://github.com/folke/ultra-runner)：跨 PHP monorepos 安装和更新 packages。

### Monorepo 的最佳管理实践
基于 monorepo 故事的集合，我们可以定义一组最佳实践：

*  定义统一的目录结构以便于查找。
*  维持分支的干净。让分支尽量的小，考虑采用基于主干的开发 (trunk-based development)。
*  为每个项目使用固定依赖。一次性升级所有依赖，强制每个项目跟上依赖。保留例外以用于真正特殊的事情。
*  如果你使用 Git，请了解如何使用 shallow clone (浅克隆) 和 filter-branch (分支过滤) 来处理大容量代码仓库。
*  货比三家，寻找像 Bazel 或 Buck 这样智能的构建系统，以加快构建和测试速度。
*  当你需要限制对某些项目的访问时，请使用 CODEOWNERS。
*  使用云 CI/CD 平台来大规模测试和部署你的应用程序。

### 需要使用 monorepos？
使用monorepo 或者 multirepo 与技术无关，而与 work culture (工作文化) 和沟通有关。因此，请根据你想要的工作方式做出决定。

本文到此结束！
---

 
原文：[What is monorepo? (and should you use it?)](https://semaphoreci.com/blog/what-is-monorepo) 译者：Echo


