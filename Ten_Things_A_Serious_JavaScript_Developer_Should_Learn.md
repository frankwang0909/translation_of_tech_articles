# Ten Things A Serious JavaScript Developer Should Learn

## 1. You should know the core language  1.熟悉 JavaScript语言的核心内容

JavaScript is not a very complicated language at its core. It can seem difficult to learn because of all of the noise around it. Browser APIs, Library APIs, jQuery, React, TypeScript, the many npm packages out there, etc all can make the language syntax space seem huge. And this isn’t helped by the fact that the language has evolved over time so you’ll see many different ways to do a single task spread out over the internet. But the core language and standard libraries are actually pretty small compared to languages like Python and Java that have more features and larger standard libraries. Eventually you’ll need the ecosystem. But knowing the core language features that are implemented in most browsers is a great start.

从 JavaScript 的核心语法来看，JavaScript 并不是一门十分复杂的编程语言。它看起来难以学习，是因为围绕它有太多的噪音。浏览器 API、库 API、jQuery、React、TypeScript、大量的 npm 包等等，它们让这门语言的语法范围看起来很庞大。这门语言的演变让你在互联网上能够看到许多不同的方式来完成某个单一的任务。这就更添乱了。但与 Python 和 Java等有着诸多特性和大量标准库的语言比起来，JavaScript 的语言核心和标准库实际上相当小。最终，你会需要 JavaScript 的生态系统，但熟悉应用于大多数浏览器中的核心语法特性，是一个不错的开始。

This means you should know about basic language features like if/else, error handling with throw/try/catch, array manipulation with push/map/reduce/filter, and string manipulation methods like replace/slice/split. You should also know about how JavaScript handles things like truthiness and falsiness, how to work with JSON data with the JSON object methods, and how variable scope works in JavaScript. These days its also becoming more and more important to have at least a basic familiarity with the more important language features that were added in ES6 like Promises, modules, classes, template literals, generators and destructuring. Even if you choose not to use those features, being able to see and understand them will help you learn faster from others.

这意味着你需要知道基本的语法特性，比如 `if/else` 、`throw/try/catch` 处理异常、`push/map/reduce/filter` 操作数组、`replace/slice/split` 处理字符串。 你也许还应该知道 JavaScript 是如何处理`真`与`假`，如何使用`JSON`对象的方法来操作 JSON 数据，以及变量的作用域。现在，熟悉 ES6 新增的重要语法特性变得越来越重要。这些特性包括 `Promises` 、`模块`、`类`、`模板字面量` 、`生成器(generators)` 以及 `解构(destructuring)` 。即使你选择不使用这些特性，理解它们可以帮助你从他人那里学得更快。

This list is mostly unordered, but this comes first because learning everything else becomes a lot harder without this knowledge, mostly because it becomes difficult to learn from other people’s code since you won’t understand what it is doing. If you’re starting from a weak base here I’d recommend finding a good book on the core language like *Eloquent JavaScript* and digging in. If you’ve been using JavaScript in an older code base or haven’t used it in a while and want to catch up, you can see my post on keeping up with new language features.

这个列表基本是没有顺序的，但这条放在第一，是因为没有这些基础知识，学习任何其他东西会变得更困难。这主要是因为你无法看懂别人的代码，难以从别人的代码中学习。如果你基础薄弱，推荐你找一本关于核心语法的书（比如 [Eloquent JavaScript](http://eloquentjavascript.net/) ）好好探究一番。

## 2. You should understand async code 理解 异步 代码

One of the biggest leaps for new JavaScript developers, even those who are experienced with other languages is the amount of asynchronous code found in idiomatic JavaScript. Asynchronous (async) code is code that is scheduled to run at a later time after some event, delay or response. It can appear a bunch of different ways in JavaScript code, and it’s important to be able to interpret them.

对于新的 JS开发者（即便是有其他语言经验的开发者），JavaScript 的异步代码是一个大的飞跃。异步（asyn）代码是指被安排在在某些事件、延迟或响应之后运行的代码。在 JavaScript 代码中有许多实现异步的方式。理解这些异步的编程方式非常重要。

This means learning about `callbacks` and `promises` at least. You should also learn about the JavaScript `event loop` and how it works Ideally some of the standard APIs that use them like setTimeout and fetch. If you’re ambitious you can learn about newer async syntax and library implementations like `async-await` or `RxJS` . But the most important thing is to spend time using and writing async code so that you get an intuitive understanding of how it works.

这意味着至少要学会`callbacks` 和`promises` 。你也应该学会 JavaScript 的 `event loop（事件循环）`以及它是如何工作的，比如  `setTimeout` 和 `fetch` 等使用事件循环的标准 API。如果你有更大的志向，可以学习更新的异步语法和实现该方式的库，比如`async-await` 或者 `RxJS`。 最重要的是，花时间编写异步代码，以便让你对于异步编程有更直观的理解。

## 3. You should get comfortable with at least one set of developer tools 熟悉至少一种开发工具

When developing JavaScript, its important to be able to debug problems and understand how existing systems work. JavaScript has a fantastic ecosystem of developer tools for diagnosing bugs and examining application code. For the most part they’re bundled with browsers (though some IDEs like VS Code and WebStorm have their own debuggers). Learning how to use these tools well will turbocharge your JS dev skills. If you can’t use them though, it will make everything much harder.

开发 JavaScript 程序时, 能够找出问题、理解现有系统是如何运作是非常重要的。JavaScript 有极好的开发工具的生态系统可以用于诊断 bug、检查应用的代码。它们大部分都是浏览器额外免费提供的（尽管一些IDE，比如VS Code 和 WebStorm， 有它们自己的调试器）。学会如何使用这些调试工具，将会提升你的 JS 开发技能。如果你不会使用这些工具，其他事情都会变得更艰难。

Competence in this means comfort with at least the JavaScript debugger panel of a browser’s devtools. You should be able to set and use breakpoints, use watch mode and the console to test for the value of expressions, and understand the stack traces. But its also good to get comfortable using the elements panel to understand and manipulate the DOM, and the network panel to debug network requests.

这方面的技能意味着至少熟悉一种浏览器开发工具的调试面板。你应该能够设置和使用断点，使用观察模式和控制台来测试表达式的值、理解堆栈踪迹。当然，如果你会使用 `元素(elements)` 面板来理解、操作DOM,  `网络(network)` 面板来调试网络请求就更好了。

## 4. You should be proficient with JavaScript functions 精通 JavaScript 的函数

Functions are the core of the JavaScript language. You should be able to use them fluently. That means understanding function scope, closures, how this works, and the difference between normal functions and arrow functions. It also means knowing about how to process arrays with function using methods like map, filter, and reduce. A good resource for “thinking in functions” is JavaScript Allonge by Reginald Braithwaite.

函数是 JavaScript 语言的核心。你应该能够熟练地使用函数。这意味着你理解函数作用域、闭包、函数如何工作以及普通函数与箭头函数的区别等等。这也意味着，你知道如何使用诸如map、filter、reduce等方法来处理数组。关于 “thinking in functions”，推荐 Reginald Braithwaite 写的 [JavaScript Allonge](https://leanpub.com/javascriptallongesix/read)。

## 5. You should be comfortable with basic design tasks 基础的设计能力

No matter how well you learn JavaScript, you’ll be limited career-wise if you can’t manage basic design tasks. This might not seem inherently true; after all isn’t that what professional designers are for? But the truth is that unless you’re a purely server-side developer (excluded by our assumptions above), there will be times when you’re asked to fill in a visual gap that a designer doesn’t have time for, or there will be ambiguities in a designers spec that aren’t worth a meeting. And learning the basics of design will help you communicate better with designers and product managers about technical constraints and design requirements.

如果你不能处理基础的设计任务，无论你 JavaScript 掌握得多好，你的职业发展都会受限。这看起来好像不太对，毕竟不是有专业的设计师吗？但事实上，除非你是纯服务端开发者，你总会遇到设计师没有时间做，或者设计规范中模拟两可但又不值得你花时间开会讨论的事情。而这时候，需要你来做些基础的设计工作。学会设计的基础知识可以帮助你与设计师和产品经理更好地沟通技术限制与设计需求。

When I talk design, I mean both the basics of implementing a visual design with HTML and CSS3, and enough of an understanding of design basics to create a simple UI that looks ok and isn’t confusing. That last requirement is harder than you’d think. Design is hard and mostly orthogonal to the set of skills most folks pick up while learning to code. For learning design theory, I’d recommend finding a simple resource on visual design first like White Space is Not Your Enemy, and ideally supplementing that with a more UX focused resource like Don’t Make Me Think. The practical side of HTML/CSS is easiest to pick up with experience. If you’re using devtools you can see what other people are doing with CSS and tweak and explore using the elements panel. And there are lots of good online resources for learning about CSS and HTML syntax like MDN for API info or CSS-Tricks for ideas, tutorials and keeping up with new stuff.

当我说设计的时候，包括实现视觉设计的 HTML、CSS基础知识以及对于设计基础有足够的理解，可以创建看起来还行且不会让人困惑的简单的 UI 元素。后一个要求比你想象的更难。设计是困难的，它是大部分开发者学习编码时都不会考虑学习的技能。学习设计理论，我推荐你先找一些简单的视觉设计的资料，比如 [White Space is Not Your Enemy](https://www.amazon.com/White-Space-Your-Enemy-Communicating/dp/0240824148/ref=sr_1_2?ie=UTF8&qid=1505198186&sr=8-2&keywords=White+Space+is+Not+Your+Enemy), 以及补充一些专注于UX的资料，比如 [Don’t Make Me Think](https://www.amazon.com/Dont-Make-Think-Revisited-Usability/dp/0321965515/ref=sr_1_1?ie=UTF8&qid=1505198386&sr=8-1&keywords=don%27t+make+me+think)。 有经验的话，HTML/CSS 实用的部分是最容易学会的。如果你使用开发工具，你可以看到其他人是怎么使用CSS，你可以使用 `elements` 面板来调试和探索。网上有大量学习CSS/HTML 语法的资源，比如在 [MDN](https://developer.mozilla.org/en-US/) 上可以查询 API 信息，或者在 [CSS-Tricks](https://css-tricks.com/) 上有很多想法、教程、以及新的技术。

## 6. You should have a basic understanding of networking and HTTP-based APIs 对于网络和基于HTTP的API 有基本的了解

Because we’re assuming that this job involves using JavaScript in a browser or other client context, you should be able to understand the basics of networking. You should know what a client and server is. You should be able to explain a rough approximation of what happens when somebody enters a URL into the browser. There’s a bunch of vocabulary thats good to know here. You should know what an HTTP request is and ideally know the conventions of what GET/PUT/POST/DELETE imply. It will help you to have a working definition of what REST is and what AJAX is. It’s good to get experience working with some HTTP based APIs, either at work or playing around with an API like Twitter or Github. You should also get familiar with at least one way to make HTTP requests in JavaScript (XMLHttpRequest, fetch, jQuery’s ajax methods).

因为我们假设这个工作包含在浏览器和其他客户端环境中使用 JavaScript， 你应该对网络有基本的理解，知道什么是客户端和服务端。你应该能够解释当用户在浏览器中输入一个 URL 后大致发生了什么。这里有大量的术语需要知道。你应该知道 HTTP 请求是什么，知道习惯上 GET/POST/PUT/DELETE 分别是什么含义。这会有助于你理解 REST 和 AJAX . 如果有基于 HTTP 的 API 使用经验就更好了，不管是在工作中还是业务玩玩诸如 Twitter 或 Github 的 API。你应该至少熟悉一种 JavaScript 发起 HTTP 请求的方法（ XMLHttpRequest，fetch, jQuery的 ajax 方法）。

## 7. You should be comfortable with Node.js based tooling 熟悉基于 Node.js 的工具

One of the biggest shifts in the last 5 years in the JavaScript world has been the explosive growth and consolidation of tools to help build JavaScript applications. Tools like eslint, Babel, Typescript, and Webpack are essential to many teams workflows. While you don’t necessarily have to be experienced writing applications for Node.js, it is important that you be able to use those tools. That means knowing how to install Node and use npm to install and update packages. It also means getting comfortable with build tools like Webpack, Rollup or Ember CLI and knowing how to use them. Reasonable people can differ on how many tools and libraries you should be using, or what the right ones to use are, but having a basic familiarity with how the Node ecosystem works has become essential.

在过去五年，JavaScript 领域最大的变化就是帮助人们构建 JavaScript 应用的工具的爆发性增长与整合。类似于 `eslint`, `Babel`, `TypeScript`,  `Webpack`的工具对于许多开发团队的工作流程非常重要。虽然你不需要有编写 Node.js 应用的经验，但会使用这个工具很重要。这意味着，你要知道如何下载安装 Node， 使用 npm 安装和更新包。同时，也意味着你要熟悉并知道如何使用 `Webpack`、`Rollup`、`Ember CLI`等构建工具。理智的人们对于应该使用多少工具和类库，哪些才是最佳的工具和类库等问题有分歧，这是很正常的。对 Node 的生态有基本的了解变得非常重要。

## 8. You should know how to use a framework to structure a moderately sized program 知道如何使用框架来组织中等规模的程序

A critical requirement for a developer who is expected to complete work with minimum supervision or hand-holding is the ability to structure their code in a maintainable way. That means having an understanding of (or helping define) the overall architecture of the system, and then writing code that fits into it. It also means being able to identify and help solve situations where the current architecture or code base norms causes problems. I put this in terms of frameworks because pretty much all medium to large JavaScript programs are using some sort of framework, whether it is an “official” framework like React, Angular, and Ember or a collection of helper files and functions cobbled together by a development team over time. For learning purposes, the popular official frameworks will usually be better to learn, since the knowledge can translate elsewhere, the purposes behind the structure are documented on the internet, and at least some sense of best practice architecture should be embedded in the code. To check this box I think it would be best to get experience with at least 2 frameworks/architectures, since it is difficult to tell which decisions a Framework makes are specific to its architecture and constraints without something to compare it with.

对于只需少量监督和手把手指导就能完成工作的开发者，一项关键的要求，是以可维护的方式组织代码的能力。这意味着理解（或者帮助定义）整个系统架构，然后编写符合这个架构的代码。同时，这也意味着，有能力发现以及帮助解决现有架构或代码库规范引起的问题。我提及框架，是因为几乎所有大、中型 的 JavaScript 程序都使用了某种框架，无论它是“正式”的框架，比如 React、Angular、Ember ，还是开发团队慢慢东拼西凑的帮助文件和函数的集合。以学习为目的的话，流行的正式框架通常更有利于学习。因为这些知识在其他地方有解释，结构背后的目的在网上有记录，并且至少最佳的架构实践的意识被镶嵌在代码里。我认为最好是能有至少两种框架/架构的经验，因为没有比较的话，就很难分辨出框架的哪些抉择是只与它的架构和限制条件有关的。

This is probably the most controversial item on this list, since I know a lot of folks believe it’s better to learn architecture ideas outside the constraints of a framework. My stance here is that frameworks are practical: they’re most likely to translate to an actual employment background and it’s easy to find practical explanations of how they solve architecture problems.

这也许是这个列表中最有争议的一项。因为我知道有许多人认为在框架的约束之外，来学习架构的思想会更好。我的立场是框架是实用的：它们很可能被理解为真实的应用场景，并且很容易找到实用的关于它们是如何解决架构问题的解释。

## 9. You should know the basics of JavaScript program performance 了解 JavaScript 程序性能的基本知识

Like design, performance is a deep well of things you can learn. But knowing the basics is important and can go a long ways. For performance “the basics” is more about process than knowledge. You need to learn how to debug slow processes and understand where the problems are coming from. For browser environments in particular there are some baseline things to understand. You’ll want to be able to tell the difference between code that is running slowly due to parsing slowness, runtime slowness, and network latency. It’s important to learn how to keep a UI responsive and not block rendering with other calculations. And it’s great to understand how to cache data and avoid making too many requests to the server.

就像设计一样，性能也是你可以深入学习的领域。了解基础的性能知识很重要，对你作用很大。性能的基础知识更多的是关于进程的。你需要学习如何调试运行慢的进程，以及理解问题来自哪里。对于浏览器环境来说，有些基础的东西需要理解。你要能分辨代码运行慢的原因，是解析时慢、运行时慢，还是网络的延迟。学会如何让 UI 元素 响应式并且不会因为其他的计算阻塞渲。理解如何缓存数据以及避免对服务端有过多的请求。

This is also an area where learning more about Computer Science theory can be beneficial. Studying data structures and algorithms can help give you good intuitions on how to handle data, and learning about Big O notation gives you a language for discussing performance tradeoffs. This is the type of background knowledge that pays off in occasional subtle ways though, not something that is always obvious applicable in the moment.

学习计算机科学的理论知识也是有益的。学习数据结构和算法，能够给你好的直接来处理数据。学习大O符号，让你有一门语言来讨论性能的妥协。这些背景知识通常是以微妙的方式起作用的。在此刻，它们不是明显的可应用的知识。

## 10. You should have a process for learning new things 学习新事物的途径

JavaScript development has changed a lot over the last 10 years, and there’s no reason to think it won’t continue to evolve over the next 10. There are also a lot of different technologies that fall under the umbrella of JavaScript, more than any one person can keep in their head. So it’s important to be able to keep up with the new technologies as they come along, and also learn about new technologies quickly when you need them. Ideally this means having at least one place where you can follow news about new JavaScript innovation. Could be Twitter, a set of blogs, newsletters or an aggregator site like Reddit or Hacker News. If you’re looking for an easy place to start, I’d recommend the JavaScript Weekly mailing list.

在过去的10年间，JavaScript 的发展发生了许多改变。我们有理由相信在未来的10年中，它会继续演化。同时，在 JavaScript 的保护伞下，也有许多不同的技术倒下了，比任何人记住的都多。所以，当新的技术出现时，及时了解它们是很重要的。当你需要的时候，快速地学会新的技术。理想的状态下，你至少有一个地方可以了解到 JavaScript 创新的新闻。可以是 Twitter、博客、简报、类似于 Reddit 或者 Hacker News 的聚合网站等。如果你在找一个简单的起点，我推荐 [JavaScript Weekly](http://javascriptweekly.com/) 的邮件列表。

You also need to get comfortable finding information about specific technologies, either to learn to use them or troubleshoot problems. These resources will likely be more varied, but it’s important to learn to Google well, and also helpful to get familiar with Stack Overflow or some other resource for getting help when you have specific questions or problems.

同时，你需要熟悉如何寻找特定技术的资讯，不管是出于学习该项技术还是解决问题的目的。这些资源可能更加多样化，但学会使用 Google 很重要， 熟悉 Stack Overflow 或者其他当你有特定的问题时可以获得帮助的地方也也有帮助。

原文链接：[Ten Things A Serious JavaScript Developer Should Learn](https://benmccormick.org/2017/07/19/ten-things-javascript/)