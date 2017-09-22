# JS工作原理：内存管理+如何处理四种常见的内存泄露

A few weeks ago we started a series aimed at digging deeper into JavaScript and how it actually works: we thought that by knowing the building blocks of JavaScript and how they come to play together you’ll be able to write better code and apps.

几周前，我们开始了旨在深挖 JS 以及 它的实际工作原理的系列文章。我们认为了解 JS 的构建单元以及它们是如何组合在一起工作的，你将能够写更好代码和应用。

The first post of the series focused on providing [an overview of the engine, the runtime, and the call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf). Thе [second post examined closely the internal parts of Google’s V8 JavaScript engine](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e) and also provided a few tips on how to write better JavaScript code.

这个系列文章的第一篇集中讨论 提供一个 [对于引擎、运行时以及调用栈的概览](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf) 。第二篇 [文章细致地审查谷歌 V8  JS 引擎的内部组成](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)  以及提供了一些如何编写更好的 JS 代码的小窍门。

In this third post, we’ll discuss another critical topic that’s getting ever more neglected by developers due to the increasing maturity and complexity of programming languages that are being used on a daily basis — memory management. We’ll also provide a few tips on how to handle memory leaks in JavaScript that we at [SessionStack](https://www.sessionstack.com/) follow as we need to make sure SessionStack causes no memory leaks or doesn’t increase the memory consumption of the web app in which we are integrated.

在这个第三篇文章中，我们将讨论另一个关键的话题--内存管理。由于日常使用的编程语言变得更加成熟和复杂，这个话题越来越被开发者所忽视。我们也将提供一些在 JS 中如何处理内存泄露的小窍门。这些窍门是我们在 SessionStack ，为了确保 SessionStack 不会引起内存泄露或者增加集成的 web app内存开销所遵循的窍门。

#### Overview 概览

Languages, like C, have low-level memory management primitives such as `malloc()` and `free()`. These primitives are used by the developer to explicitly allocate and free memory from and to the operating system.

像 C 这样的编程语言，有比较低级的内存管理的 primitives，比如 `malloc()` 和 `free()`。 开发者使用这些 primitives 显式地从操作系统申请分配内存以及释放内存给操作系统。

At the same time, JavaScript allocates memory when things (objects, strings, etc.) are created and “automatically” frees it up when they are not used anymore, a process called *garbage collection*. This seemingly “automatical” nature of freeing up resources is a source of confusion and gives JavaScript (and other high-level-language) developers the false impression they can choose not to care about memory management. **This is a big mistake.**

与此同时，创建事物（对象、字符串等）时， JS 会分配内存。当它们不再使用时，JS 会“自动地” 释放内存， 这个过程被称为 *垃圾回收*。这个看起来“自动地”释放资源的特性是困惑的来源，它给了 JS (甚至是其他的高级语言) 开发者一个错误的印象，以为可以不用考虑内存管理。**这是一个巨大的错误**。

Even when working with high-level languages, developers should have an understanding of memory management (or at least the basics). Sometimes there are issues with the automatic memory management (such as bugs or implementation limitations in the garbage collectors, etc.) which developers have to understand in order to handle them properly (or to find a proper workaround, with a minimum trade off and code debt).

即使是使用高级编程语言，开发者也应该了解内存管理（至少知道基本原理）。有时，开发者需要了解自动内存管理的问题（比如 垃圾回收器的 bug 或者使用限制）才能正确地处理这些问题（或者以最小的代价和技术债，找出一个恰当的变通方法）。

#### Memory life cycle  内存生命周期

No matter what programming language you’re using, memory life cycle is pretty much always the same:

无论你正在使用哪种编程语言，内存的生命周期都差不多：

![img](https://cdn-images-1.medium.com/max/800/1*slxXgq_TO38TgtoKpWa_jQ.png)

Here is an overview of what happens at each step of the cycle:

以下是这个循环的每一步发生的概览：

- **Allocate memory **— memory is allocated by the operating system which allows your program to use it. In low-level languages (e.g. C) this is an explicit operation that you as a developer should handle. In high-level languages, however, this is taken care of for you.

  分配内存 --  你的程序使用操作系统分配的内存。在低级语言（比如 C ）中，作为一个开发者需要处理的一个显式的操作。然而，高级语言已经为你处理好这个过程了。

- **Use memory — **this is the time when your program actually makes use of the previously allocated memory. **Read** and **write** operations are taking place as you’re using the allocated variables in your code.

  使用内存 -- 这个阶段，你的程序真正的在使用之前分配的内存。当你在代码中使用分配的变量时，发生读、写操作。

- **Release memory** — now is the time to release the entire memory that you don’t need so that it can become free and available again. As with the **Allocate memory **operation, this one is explicit in low-level languages.

  释放内存 -- 这个阶段是释放不再需要的所有内存，这些内存变成空闲的，可以再次使用。与 分配内存 操作一样，这个步骤在低级语言中是显式的。

For a quick overview of the concepts of the call stack and the memory heap, you can read our [first post on the topic](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf).

关于快速浏览调用栈和内存堆的概念，你可以阅读我们[这个话题的第一篇文章](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf) 。

#### What is memory?  什么是内存？

Before jumping straight to memory in JavaScript, we’ll briefly discuss what memory is in general and how it works in a nutshell.

在直接讨论 JS 的内存之前，我们简单地讨论内存是什么，以及它的工作原理。

On a hardware level, computer memory consists of a large number of
[flip flops](https://en.wikipedia.org/wiki/Flip-flop_%28electronics%29). Each flip flop contains a few transistors and is capable of storing one bit. Individual flip flops are addressable by a **unique identifier**, so we can read and overwrite them. Thus, conceptually, we can think of our entire computer memory as a just one giant array of bits that we can read and write.

在硬件层面上，电脑内存由大量的[触发器](https://en.wikipedia.org/wiki/Flip-flop_%28electronics%29) 组成。每一个触发器包含一些晶体管，它能够储存一个字节。每个触发器可以由唯一的标识符可寻址，所以我们可以读取或重写它们。因此，概念上，我们可以把整个计算机内存当做一个巨大的可以读写的字节数组。

Since as humans, we are not that good at doing all of our thinking and arithmetic in bits, we organize them into larger groups, which together can be used to represent numbers. 8 bits are called 1 byte. Beyond bytes, there are words (which are sometimes 16, sometimes 32 bits).

因为我们人类不擅长以比特（二进制位）的形式来思考和处理算法，所以我们将比特编组在更大的群组中。这个更大的群组可以用来表示数字。8个比特为1个字节。除了字节，还有字（16位或32位的）。

A lot of things are stored in this memory:

许多东西存储在内存中：

1. All variables and other data used by all programs.

   所有程序用到的所有的变量和其他数据。

2. The programs’ code, including the operating system’s.

   程序代码，包括操作系统的程序代码。

The compiler and the operating system work together to take care of most of the memory management for you, but we recommend that you take a look at what’s going on under the hood.

编译器和操作系统一起为你处理了大部分的内存管理，但我们建议你看一看它的底层工作原理。

When you compile your code, the compiler can examine primitive data types and calculate ahead of time how much memory they will need. The required amount is then allocated to the program in the call **stack space**. The space in which these variables are allocated is called the stack space because as functions get called, their memory gets added on top of the existing memory. As they terminate, they are removed in a LIFO (last-in, first-out) order. For example, consider the following declarations:

当你编译代码时，编译器能够检查原始数据类型并且提前计算它们所需的内存。在调用堆栈空间时，操作系统将所需的内存空间分配给程序。这些变量分配到的空间，被称为 **栈内存（stack space）**， 因为调用函数时，分配给它们的内存添加到已有内存的顶部。当函数运行结束时，栈内存以 LIFO (后进先出) 的方式被移除。例如：

```
int n; // 4 bytes
int x[4]; // array of 4 elements, each 4 bytes
double m; // 8 bytes
```

The compiler can immediately see that the code requires 

4 + 4 × 4 + 8 = 28 bytes.

编译器可以立即看出上述代码需要 28个字节。

> That’s how it works with the current sizes for integers and doubles. About 20 years ago, integers were typically 2 bytes, and double 4 bytes. Your code should never have to depend on what is at this moment the size of the basic data types.
>
> 这是以 int和 double 目前的大小计算的。大约20年前，int 是2个字节的，double 是4个字节的。你的代码不会受那个年代的基本数据类型大小的影响。

The compiler will insert code that will interact with the operating system to request the necessary number of bytes on the stack for your variables to be stored.

编译器将添加与操作系统交互的代码，在栈中，为存储你的变量申请必要数量的字节。

In the example above, the compiler knows the exact memory address of each variable. In fact, whenever we write to the variable `n`, this gets translated into something like “memory address 4127963” internally.

在上述例子中，编译器知道每一个变量的精确的内存地址。事实上，当我们写入一个变量` n` 时，它在内部被转成了类似于“内存地址 4127963”的东西。

Notice that if we attempted to access `x[4]` here, we would have accessed the data associated with m . That’s because we’re accessing an element in the array that doesn’t exist — it’s 4 bytes further than the last actual allocated element in the array which is `x[3]`, and may end up reading (or overwriting) some of `m`’s bits. This would almost certainly have very undesired consequences for the rest of the program.

注意，如果我们试图获得 `x[4]`，我们将会获取与`m` 相关的数据。因为我们试图在数组中获取一个不存在、比数组中最后一个真实分配的元素（`x[3]` ）多4个字节的元素。这个操作可能会导致读取（或者重写）`m` 的一部分二进制位数据。同时，这也很可能队程序的其他部分造成不期望看到的后果。

![img](https://cdn-images-1.medium.com/max/800/1*5aBou4onl1B8xlgwoGTDOg.png)

When functions call other functions, each gets its own chunk of the stack when it is called. It keeps all its local variables there, but also a program counter that remembers where in its execution it was. When the function finishes, its memory block is once again made available for other purposes.

当函数调用其他函数时，每个函数在被调用时获得它自己的堆栈空间，并将它的本地变量存放于此。同时，程序计数器记住每一次执行的地方。当函数结束时，它的内存空间释放出来，可再次用于其他用途。

#### Dynamic allocation  动态的内存分配

Unfortunately, things aren’t quite as easy when we don’t know at compile time how much memory a variable will need. Suppose we want to do something like the following:

很不幸地是，当我们在编译时不知道变量需要多少内存时，事情就没那么简单了。假设我们想要做如下事情：

```
int n = readInput(); // reads input from the user
...
// create an array with "n" elements
```

Here, at compile time, the compiler does not know how much memory the array will need because it is determined by the value provided by the user.

在编译时，编译器不知道 这个数组需要多少内存，因为它取决于用于输入的值。

It, therefore, cannot allocate room for a variable on the stack. Instead, our program needs to explicitly ask the operating system for the right amount of space at run-time. This memory is assigned from the **heap space**. The difference between static and dynamic memory allocation is summarized in the following table:

因此，操作系统无法为变量在 **栈** 中分配空间。相反，我们的程序需要在运行时明确地向操作系统请求正确数量的空间。这个内存从 **堆空间** 分配。静态内存分配与动态内存分配的区别归纳在下面的表格中：

![img](https://cdn-images-1.medium.com/max/800/1*qY-yRQWGI-DLS3zRHYHm9A.png)											     							Differences between statically and dynamically allocated memory.       

​							静态内存分配与动态内存分配的区别

To fully understand how dynamic memory allocation works, we need to spend more time on **pointers, **which might be a bit too much of a deviation from the topic of this post. If you’re interested in learning more, just let me know in the comments and we can go into more details about pointers in a future post.

要想完全理解动态内存分配的工作原理，我们需要花更多的时间在 **指针**（**pointers** ）。 那样就偏离了这篇文章的主题。如果你有兴趣学习，在评论区留言让我知道，我们可以在将来的文章中详细讨论指针。

#### Allocation in JavaScript       JS 的内存分配

Now we’ll explain how the first step (allocate memory) works in JavaScript.

现在，我们将解释在 JS 中第一步（分配内存） 是如何工作的。

JavaScript relieves developers from the responsibility to handle memory allocations — JavaScript does it by itself, alongside declaring values.

JS 把开发者从处理内存分配的责任重解脱出来 ——在声明变量的时候， JS 自己做了内存分配的工作。

```javascript
// allocates memory for a number   为数字分配内存
var n = 374; 

// allocates memory for a string  为字符串分配内存
var s = 'sessionstack'; 

// allocates memory for an object and its contained values
// 为对象及其属性分配内存
var o = {
  a: 1,
  b: null
}; 

// (like object) allocates memory for the array and its contained values
// （类似于对象）为数组及其成员 分配内存
var a = [1, null, 'str']; 

// allocates a function (which is a callable object)
// 为函数（即可调用的对象）分配内存
function f(a) {
  return a + 3;
} 

// function expressions also allocate an object
// 函数表达式也为对象分配内存
someElement.addEventListener('click', function() {
  someElement.style.backgroundColor = 'blue';
}, false);
```



Some function calls result in object allocation as well:

某些函数的调用也会导致分配内存给对象：

```javascript
var d = new Date(); // allocates a Date object  给一个 Date 对象分配内存

var e = document.createElement('div'); // allocates a DOM element 给一个 DOM 元素分配内存
```



Methods can allocate new values or objects:

方法为新的值或对象分配内存：

```javascript
var s1 = 'sessionstack';
var s2 = s1.substr(0, 3); // s2 is a new string  s2是一个新的字符串
// Since strings are immutable, JavaScript may decide to not allocate memory, but just store the [0, 3] range.
// 由于字符串是不可改变的，JS 也许会决定不分配内存，而是仅仅存储 [0, 3] 范围。

var a1 = ['str1', 'str2'];
var a2 = ['str3', 'str4'];
var a3 = a1.concat(a2); 
// new array with 4 elements being the concatenation of a1 and a2 elements
// 新的数组的四个成员是有 a1 和 a2 成员合并而成的
```



#### Using memory in JavaScript      JS 的内存使用

Using the allocated memory in JavaScript basically, means reading and writing in it.

在 JS 中使用分配的内存，基本上意味着在该内存中的读、写操作。

This can be done by reading or writing the value of a variable or an object property or even passing an argument to a function.

这些操作可以是读取或者写入一个变量或对象属性的值，甚至是给函数传递参数。

#### Release when the memory is not needed anymore  当内存不再需要时，释放内存

Most of the memory management issues come at this stage. 

大部分的内存管理的问题发生在这一阶段。

The hardest task here is to figure out when the allocated memory is not needed any longer. It often requires the developer to determine where in the program such piece of memory is not needed anymore and free it.

最难的是确定分配的内存在何时不再需要用到。通常，这需要开发者弄明白这块内存在程序中的哪个地方是不再需要的，因而可以释放这块内存。

High-level languages embed a piece of software called **garbage collector** which job is to track memory allocation and use in order to find when a piece of allocated memory is not needed any longer in which case, it will automatically free it.

高级语言自带 **垃圾回收器**，它的任务就是追踪内存的分配和使用，以便找出在何时不再需要使用到某一段已分配的内存，从而可以自动地释放这一部分内存。

Unfortunately, this process is an approximation since the general problem of knowing whether some piece of memory is needed is [undecidable](http://en.wikipedia.org/wiki/Decidability_%28logic%29) (can’t be solved by an algorithm).

不幸地是，由于获知某些内存是否需要的不可判定性（不能有算法解决）这个普遍的问题，这个垃圾回收的过程是近似的。

Most garbage collectors work by collecting memory which can no longer be accessed, e.g. all variables pointing to it went out of scope. That’s, however, an under-approximation of the set of memory spaces that can be collected, because at any point a memory location may still have a variable pointing to it in scope, yet it will never be accessed again.

大部分的垃圾回收器 收集不再访问的内存，例如所有指向该内存的变量都已超出作用域。这个收集的内存小于可以被回收的内存空间集合近似值。因为在任意时刻，即便某一内存地址不再被访问，但可能仍有作用域内的变量指向这个内存地址。

#### Garbage collection  垃圾回收

Due to the fact that finding whether some memory is “not needed anymore” is undecidable, garbage collections implement a restriction of a solution to the general problem. This section will explain the necessary notions to understand the main garbage collection algorithms and their limitations.

由于确定某些内存是否“不再需要” 是不可判定的，针对这个普通的问题的解决方案，垃圾回收做了一些限定。这一节将解释一些必要的概念用于理解主要的垃圾回收算法和他们的限制条件。

#### Memory references  内存引用

The main concept garbage collection algorithms rely on is the one of **reference**.

垃圾回收算法的主要概念依赖于 **引用**。

Within the context of memory management, an object is said to reference another object if the former has an access to the latter (can be implicit or explicit). For instance, a JavaScript object has a reference to its [prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain)(**implicit reference**) and to its properties’ values (**explicit reference**).

在内存管理的上下文中，一个对象是另一个对象的引用，如果前者能够（隐式地或显式地）访问到后者。例如，一个 JS 对象有它的 prototype 的引用（隐式的引用）和 它的属性值的引用（显示的引用）。

In this context, the idea of an “object” is extended to something broader than regular JavaScript objects and also contains function scopes (or the global **lexical scope**).

在这个上下文中，“object”的概念 比普通的 JS 对象有更广泛的含义，同时包含函数作用域（或全局**词法作用域**）。

> Lexical Scoping defines how variable names are resolved in nested functions: inner functions contain the scope of parent functions even if the parent function has returned.
>
> 词法作用域定义了在嵌套的函数中，变量名是如何解决的：内部函数包含父函数的作用域，即使父函数已返回。

#### Reference-counting garbage collection  引用计数垃圾回收

This is the simplest garbage collection algorithm. An object is considered “garbage collectible” if there are **zero** references pointing to it.

这是最简单的垃圾回收算法。如果零个引用指向这个对象，那么这个对象被认为是“可以回收的垃圾”。

Take a look at the following code:

看一看以下的示例代码：

```javascript
var o1 = {
  o2: {
    x: 1
  }
};
// 2 objects are created. 
// 创建了两个对象
// 'o2' is referenced by 'o1' object as one of its properties. 
// 'o2'作为对象'o1'的属性，被对象'o1'引用
// None can be garbage-collected 
// 两个对象都不可以被垃圾回收

var o3 = o1; 
// the 'o3' variable is the second thing that has a reference to the object pointed by 'o1'. 
// 'o3' 是第二个有对象'o1'的引用的变量

o1 = 1;      
// now, the object that was originally in 'o1' has a single reference, embodied by the 'o3' variable
// 现在，原先在 'o1' 的对象 有一个单独的引用，即变量 'o3'

var o4 = o3.o2; 
// reference to 'o2' property of the object.
// 对象（'o3'）的属性'o2'的引用
// This object has now 2 references: one as a property. The other as the 'o4' variable
// 这个对象（'o2'）现在有两个引用，其中一个作为属性（对象'o3'的属性），另一个作为变量 'o4'。

o3 = '374'; 
// The object that was originally in 'o1' has now zero references to it. 
// 原先在 'o1'的对象，现在没有指向它引用了。
// It can be garbage-collected.
// 它可以被垃圾回收。
// However, what was its 'o2' property is still referenced by the 'o4' variable, so it cannot be freed.
// 然而，它的属性'o2'仍然被变量'o4'引用，所以它不能被释放。

o4 = null; 
// what was the 'o2' property of the object originally in 'o1' has zero references to it. 
// It can be garbage collected.
// 原先在'o1'中的对象的属性'o2' 没有指向它的引用了。它可以被垃圾回收了。
```



#### Cycles are creating problems   循环 产生问题

There is a limitation when it comes to cycles. In the following example, two objects are created and reference one another, thus creating a cycle. They will go out of scope after the function call, so they are effectively useless and could be freed. However, the reference-counting algorithm considers that since each of the two objects is referenced at least once, neither can be garbage-collected.

当涉及到循环时，就有局限性。在接下来的例子中，创建了两个对象，它们相互引用对方，即造成了循环引用。在函数调用后，它们会超出作用域，所以它们没有用途可以被释放。但是，引用计数算法认为既然每个对象都至少被引用了一次，这两个对象都无法被垃圾回收。

```javascript
function f() {
  var o1 = {};
  var o2 = {};
  o1.p = o2; // o1 references o2
  o2.p = o1; // o2 references o1. This creates a cycle.
}
f();
```

![img](https://cdn-images-1.medium.com/max/800/1*GF3p99CQPZkX3UkgyVKSHw.png)

#### Mark-and-sweep algorithm   标记-清除 算法

In order to decide whether an object is needed, this algorithm determines whether the object is reachable.

为了确定一个对象是否还需要被使用到，这个算法检查该对象是否可以获得。

The algorithm consists of the following steps:

这个算法由以下几个步骤组成：

1. The garbage collector builds a list of  “roots”  .  Roots usually are global variables to which a reference is kept in the code. In JavaScript, the “window” object is an example of a global variable that can act as a root.

   垃圾回收器创建了一个"roots" 列表。Roots 通常是全局变量，它们的引用被保留在代码中。在 JS 中，“window” 对象作为一个全局变量的例子，可以被看做是一个 “root”。

2. All roots are inspected and marked as active (i.e. not garbage). All children are inspected recursively as well. Everything that can be reached from a root is not considered garbage.

   检测并标记所有的“roots"为 active（即不是垃圾）。所有的子元素也递归地进行检测。所有能从“root” 获得的，都不被认为是垃圾。

3. All pieces of memory not marked as active can now be considered garbage. The collector can now free that memory and return it to the OS.

   所有没有被标记为 active 的内存片段，现在可以被认为是垃圾。此时，垃圾回收器可以是否这段内存，并把它返回给操作系统。

   ![img](https://cdn-images-1.medium.com/max/800/1*WVtok3BV0NgU95mpxk9CNg.gif)			         A visualization of the mark and sweep algorithm in action

   ​                                    **标记-清除** 算法 的示意图

This algorithm is better than the previous one since “an object has zero reference” leads to this object being unreachable. The opposite is not true as we have seen with cycles.

因为“一个对象没有引用”使得这个对象无法被获取到，这个  **标记-清除** 算法比之前那个 引用计数 算法好。既然我们已经见识了 循环引用的问题，我们就知道反过来（ 引用计数 算法比 标记清除算法好）不是真的。

As of 2012, all modern browsers ship a mark-and-sweep garbage-collector. All improvements made in the field of JavaScript garbage collection (generational/incremental/concurrent/parallel garbage collection) over the last years are implementation improvements of this algorithm (mark-and-sweep), but not improvements over the garbage collection algorithm itself, nor its goal of deciding whether an object is reachable or not. 

截止 2012 年，所有的现代浏览器都部署了 标记-清除 垃圾回收器。在过去的几年中，所有在 JS 垃圾回收领域内的改进（generational/incremental/concurrent/parallel garbage collection）都是对这个 标记-清除 算法 的改进的运用，而不是对于垃圾回收算法本身的改进运用，也不是对于决定一个对象是否可以获得的（reachable） 目标的运用。

[In this article](https://en.wikipedia.org/wiki/Tracing_garbage_collection), you can read in a greater detail about tracing garbage collection that also covers mark-and-sweep along with its optimizations.

[这篇文章](https://en.wikipedia.org/wiki/Tracing_garbage_collection)，你可以阅读到关于追踪垃圾回收的细节的更多内容、以及 **标记-清除** 算法及其优化等内容。

#### Cycles are not a problem anymore  循环不再是问题了

In the first example above, after the function call returns, the two objects are not referenced anymore by something reachable from the global object. Consequently, they will be found unreachable by the garbage collector. 

在第一个例子中，在函数被调用返回后，这两个对象不再被全局对象可访问到的东西所引用。因此，垃圾回收器会认为这两个对象是不可访问的（unreachable）。

![img](https://cdn-images-1.medium.com/max/800/1*FbbOG9mcqWZtNajjDO6SaA.png)

Even though there are references between the objects, they’re not reachable from the root.

即使 对象之间有引用，它们是不能从 “root” 访问到的。

#### Counter intuitive behavior of Garbage Collectors 垃圾回收器的反直觉行为

Although Garbage Collectors are convenient they come with their own set of trade-offs. One of them is *non-determinism*. In other words, GCs are unpredictable. You can’t really tell when a collection will be performed. This means that in some cases programs use more memory that it’s actually required. In other cases, short-pauses may be noticeable in particularly sensitive applications. Although non-determinism means one cannot be certain when a collection will be performed, most GC implementations share the common pattern of doing collection passes during allocation. If no allocations are performed, most GCs stay idle. Consider the following scenario:

虽然垃圾回收器很方便实用，但它们也有一些自己的权衡妥协，其中之一就是 *不确定性*  （non-determinism）。换言之，垃圾回收器是不可预测的。你无法真正知道什么时候执行垃圾回收。这意味着，在某些情况下，程序会使用比它真正需要的更多的内存。在另一些情况下，短暂的停顿也许在特别灵敏的应用中能够被注意到。尽管不确定性意味着没有人能够确定什么时候执行垃圾回收，但大部分的垃圾回收的执行有与分配内存时收集参数的行为相同的特征。如果没有执行内存分配，大部分垃圾回收器处在闲置状态。看看以下场景：

1. A sizable set of allocations is performed.

   执行大量的内存分配操作。

2. Most of these elements (or all of them) are marked as unreachable (suppose we null a reference pointing to a cache we no longer need).

   这些元素大部分的（或者所有）被标记为不可访问的（假设我们设置指向不再需要的缓存的引用为null）。

3. No further allocations are performed.

   没有执行更多的内存分配。

In this scenario, most GCs will not run any further collection passes. In other words, even though there are unreachable references available for collection, these are not claimed by the collector. These are not strictly leaks but still, result in higher-than-usual memory usage.

在这个场景中，大部分的垃圾回收器不会执行更多的参数收集。换言之，即使有不可访问的引用可以收集，垃圾回收器也不会去收集这些引用。它们不是严格意义上的泄露，但仍然导致了比平常更高的内存使用。

#### What are memory leaks?  什么是内存泄露？

In essence, memory leaks can be defined as memory that is not required by the application anymore but for some reason is not returned to the operating system or the pool of free memory.

本质上，内存泄露定义为应用程序不再需要的内存因为某种原因没有返还给操作系统或空闲的内存池。



![img](https://cdn-images-1.medium.com/max/800/1*0B-dAUOH7NrcCDP6GhKHQw.jpeg)

Programming languages favor different ways of managing memory. However, whether a certain piece of memory is used or not is actually an [undecidable problem](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#Release_when_the_memory_is_not_needed_anymore). In other words, only developers can make it clear whether a piece of memory can be returned to the operating system or not.

编程语言支持不同的管理内存的方式。然而，实际上，某个内存片段是否被使用是一个[不可判定的问题](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#Release_when_the_memory_is_not_needed_anymore)。换言之，只有开发者能够弄清楚某一个内存片段是否能被返还给操作系统。

Certain programming languages provide features that help developers do this. Others expect developers to be completely explicit about when a piece of memory is unused. Wikipedia has good articles on [manual](https://en.wikipedia.org/wiki/Manual_memory_management) and [automatic](https://en.wikipedia.org/wiki/Manual_memory_management)memory management.

一些编程语言提供了一些功能，帮助开发者做这件事情。其他的编程语言则期望开发者完全清楚一段内存在什么时候是没被使用的。维基百科有很棒的关于[手动](https://en.wikipedia.org/wiki/Manual_memory_management)和[自动](https://en.wikipedia.org/wiki/Manual_memory_management)内存管理的文章。

#### The four types of common JavaScript leaks  四种常见的 JS 内存泄露

#### 1: Global variables  全局变量

JavaScript handles undeclared variables in an interesting way: a reference to an undeclared variable creates a new variable inside the *global* object. In the case of browsers, the global object is `window`. In other words:

JS 以一种有趣的方式来处理未声明的变量：对一个未声明变量的引用会在*全局对象*中创建一个新的变量。在浏览器环境中，全局对象为 `window` 。 换言之，

```javascript
function foo(arg) {
    bar = "some text";
}
```

is the equivalent of:

等价于：

```javascript
function foo(arg) {
    window.bar = "some text";
}
```

If `bar` was supposed to hold a reference to a variable only inside the scope of the `foo` function and you forget to use `var` to declare it, an unexpected global variable is created.

如果 `bar` 本应该保持一个仅在 函数 `foo` 作用域中的变量的引用，但你忘记使用`var` 来声明这个变量，将会意外地创建一个全局变量。

In this example, leaking a simple string won't do much harm, but it could certainly be worse.

在这个例子中，泄露一个简单的字符串不会造成多大的伤害，但有可能更糟糕的事。

Another way in which an accidental global variable can be created is through `this`:

另一个创建意外的全局变量的方式是通过`this`：

```javascript
function foo() {
    this.var1 = "potential accidental global";
}
// `foo` called on its own, this points to the global object (window) rather than being undefined.
// 调用函数 foo 时，`this` 指向全局对象（window） 而不是`undefined`
foo();
```

> To prevent these mistakes from happening, add `'use strict';` at the beginning of your JavaScript files. This enables a stricter mode of parsing JavaScript that prevents accidental global variables. [Learn more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) about this mode of JavaScript execution.
>
> 为了防止出现这个错误，在 你的 JS 文件中的开头添加`  'use strict';  `。这样就开启了 解析 JS 的严格模式 ，可以防止产生意外的全局变量。

Even though we talk about unsuspected globals, it’s still the case that much code is filled with explicit global variables. These are by definition non-collectible (unless assigned as null or reassigned). In particular, global variables that are used to temporarily store and process big amounts of information are of concern. If you must use a global variable to store lots of data, make sure to **assign it as null or reassign it**  after you are done with it.

虽然我们讨论的是未预料到的全局变量，仍然会有代码里充斥着明确的全局变量的时候。除非赋值为`null` 或者重新赋值，不然这些全局变量就是不可回收的（non-collectible）。尤其是用于临时储存和处理大量信息的全局变量很重要。如果你使用一个全局变量来储存大量的数据，确保在使用完之后，给它赋值为`null` 或重新赋值。

#### 2: Timers or callbacks that are forgotten 被遗忘的定时器或者回调函数

The use of `setInterval` is quite common in JavaScript.

在 JS 中， `setInterval` 的使用十分常见。

Most libraries, that provide observers and other facilities that take callbacks, take care of making any references to the callback unreachable after their own instances become unreachable as well. In the case of`setInterval`, however, code like this is quite common:

大部分的库提供观察者以及其他工具来处理回调函数。在实例变得不可访问时，将回调函数的任何引用都处理成不可访问。在 `setInterval` 中，像这样的代码十分常见：

```javascript
var serverData = loadData();
setInterval(function() {
    var renderer = document.getElementById('renderer');
    if(renderer) {
        renderer.innerHTML = JSON.stringify(serverData);
    }
}, 5000); //This will be executed every ~5 seconds.
```

This example illustrates what can happen with timers: timers that make reference to nodes or data that is no longer required.

这个例子解释了定时器的作用：定时器是的程序不再需要 node 节点和数据的引用。

The object represented by `renderer` may be removed in the future, making the whole block inside the interval handler unnecessary. However, the handler cannot be collected as the interval is still active, (the interval needs to be stopped for this to happen). If the interval handler cannot be collected, its dependencies cannot be collected either. This means that `serverData`, which presumably stores quite a big amount of data, cannot be collected either.

`renderer` 代表的对象可能在未来会被移除，那么定时器处理函数内的整个代码块将变得不再被需要。然而，处理函数不能被垃圾回收，因为定时器还在运行（定时器需要停止才能被垃圾回收）。如果定时器的处理函数不能被垃圾回收，它的依赖也不会被垃圾回收。即意味着可能存储大量数据的 `serverData` 也不能被垃圾回收。

In the case of observers, it is important to make explicit calls to remove them once they are not needed anymore (or the associated object is about to be made unreachable).

在观察者模式中，一旦观察者不再被需要（或者与之相关的对象即将变成不可访问），显示地调用观察者模式提供的方法去移除观察者是很重要的。

In the past, this used to be particularly important as certain browsers (the good old IE 6) were not able to manage well cyclic references (see below for more info). Nowadays, most browsers can and will collect observer handlers once the observed object becomes unreachable, even if the listener is not explicitly removed. It remains good practice, however, to explicitly remove these observers before the object is disposed of. For instance:

在过去，这种方式对于某些浏览器（老式的 IE 6）不能很好地处理循环的引用（更多信息见下方）。现在，大多数浏览器能够在观察的对象变成不可访问时，立即回收观察者处理函数，即使监听还没有显式地移除。

```javascript
var element = document.getElementById('launch-button');
var counter = 0;
function onClick(event) {
   counter++;
   element.innerHtml = 'text ' + counter;
}
element.addEventListener('click', onClick);
// Do stuff
element.removeEventListener('click', onClick);
element.parentNode.removeChild(element);
// Now when element goes out of scope,
// both element and onClick will be collected even in old browsers 
// that don't handle cycles well.
```



Nowadays, modern browsers (including Internet Explorer and Microsoft Edge) use modern garbage collection algorithms that can detect these cycles and deal with them correctly. In other words, it’s not strictly necessary to call `removeEventListener` before making a node unreachable.

现在，现代浏览器（包括 IE 和 微软 Edge）使用现代的垃圾回收算法，能够检测这些循环并正确地处理它们。换言之，在使得 node 节点不可访问之前，可以不需要调用 `removeEventListener` 。

Frameworks and libraries such as *jQuery* do remove listeners before disposing of a node (when using their specific APIs for that). This is handled internally by the libraries which also make sure that no leaks are produced, even when running under problematic browsers such as … yeah, IE 6.

框架和库，比如 `jQuery`，  在 (使用它们特定的 API) 销毁一个 node 节点前，确实会移除事件监听。这个过程在库的内部处理，同时确保不会产生内存泄露，即便是运行在有问题的浏览器，比如 IE 6。

#### 3: Closures  闭包

A key aspect of JavaScript development are closures: an inner function that has access to the outer (enclosing) function’s variables. Due to the implementation details of the JavaScript runtime, it is possible to leak memory in the following way:

JS 开发的 重要的特性之一是闭包：内部函数可以访问包裹它的外部函数的变量。由于 JS 运行时的执行细节，以下方式很可能出现内存的泄露。：

```javascript
var theThing = null;
var replaceThing = function () {
  var originalThing = theThing;
  var unused = function () {
    if (originalThing){ // a reference to 'originalThing'
      console.log("hi");
    }
  };
  theThing = {
    longStr: new Array(1000000).join('*'),
    someMethod: function () {
    	console.log("message");
    }
  };
};
setInterval(replaceThing, 1000);   
```



This snippet does one thing: every time `replaceThing` is called, `theThing` gets a new object which contains a big array and a new closure (`someMethod`). At the same time, the variable `unused` holds a closure that has a reference to `originalThing` (`theThing` from the previous call to `replaceThing`). Already somewhat confusing, huh? The important thing is that **once a scope is created for closures that are in the same parent scope, that scope is shared**.

这段代码做了一件事情：每次调用 `replaceThing` , `theThing` 获取一个新的对象，包含一个很大的数组和新的闭包（`someMethod`）。 同时，变量`unused` 存储一个闭包。这个闭包有一个指向 `originalThing` 的引用（来自前一次调用`replaceThing`得到的`theThing`）。已经有些晕了？

最重要的是，**一旦在同一个父级作用域为一个闭包创建了作用域，这个作用域就是共享的**。

In this case, the scope created for the closure `someMethod` is shared with`unused`. `unused` has a reference to `originalThing`. Even though `unused` is never used, `someMethod  ` can be used through `theThing` outside of the scope of `replaceThing` (e.g. somewhere globally). And as `someMethod` shares the closure scope with `unused`, the reference `unused` has to `originalThing` forces it to stay active (the whole shared scope between the two closures). This prevents its collection.

在这种情况下，为闭包 `someMethod` 创建的作用域与 `unused` 是共享的。`unused` 有一个指向 `originalThing` 的引用。即使 `unused` 没被使用，我们仍然能够通过 `theThing`在`replaceThing` 的作用域之外（比如全局作用域的某个地方）使用 `someMethod` 。由于 `someMethod` 与 `unused` 共享闭包作用域， `unused` 指向 `originalThing`  的引用被强制保持激活状态（在两个闭包之间共享的作用域内）。这就阻止了 `unused` 的垃圾回收。

When this snippet is run repeatedly a steady increase in memory usage can be observed. This does not get smaller when the GC runs. In essence, a linked list of closures is created (with its root in the form of the `theThing` variable), and each of these closures' scopes carries an indirect reference to the big array, resulting in a sizable leak.

当这段代码重复运行时，能够观察到内存的使用在稳定地增加。当垃圾回收器运行时，这里内存的使用也不会变小。实质上，一连串相关的闭包被创建（以及 以变量 `theThing` 形式存在的根？），每一个闭包的作用域包含了一个指向这个大数组的直接引用，从而导致相当大的内存泄露。

This issue was found by the Meteor team and [they have a great article](https://blog.meteor.com/an-interesting-kind-of-javascript-memory-leak-8b47d2e7f156) that describes the issue in great detail. 

这个问题是由 Meteor 团队发现的，他们有[一个篇很棒的文章](https://blog.meteor.com/an-interesting-kind-of-javascript-memory-leak-8b47d2e7f156)详细地描述了这个问题。

#### 4: Out of DOM references  出自 DOM 引用

Sometimes it may be useful to store DOM nodes inside data structures. Suppose you want to rapidly update the contents of several rows in a table. It may make sense to store a reference to each DOM row in a dictionary or an array. When this happens, two references to the same DOM element are kept: one in the DOM tree and the other in the dictionary. If at some point in the future you decide to remove these rows, you need to make both references unreachable.

有时候，在数据结构中存储 DOM 节点很有用。假设你想在 一个表格中快速地更新几行的内容，以字典或者数组的形式存储每一行 DOM 的引用是合理的。这时，保存了两个同一个 DOM 元素的引用，一个在 DOM 树种， 另一个在字典中。如果未来的某个时间点，你决定移除这几行（内容），你需要使这两个引用都不可访问。

```javascript
var elements = {
    button: document.getElementById('button'),
    image: document.getElementById('image')
};

function doStuff() {
    image.src = 'http://example.com/image_name.png';
}

function removeImage() {
    // The image is a direct child of the body element.
    // image 是 body 元素的直接子元素。
    document.body.removeChild(document.getElementById('image'));
    // At this point, we still have a reference to #button in the
    //global elements object. In other words, the button element is
    //still in memory and cannot be collected by the GC.
    // 此时，在全局的 elements 对象中，还有一个 指向 #button 的引用。
    // 换言之，这个 button 元素还在内存中，不能够被 GC 回收。
}
```



There’s an additional consideration that has to be taken into account when it comes to references to inner or leaf nodes inside a DOM tree. Say you keep a reference to a specific cell of a table (a `<td>` tag) in your JavaScript code. One day you decide to remove the table from the DOM but keep the reference to that cell. Intuitively one may suppose the GC will collect everything but that cell. In reality, this won’t happen: the cell is a child node of that table and children keep references to their parents. That is, the reference to the table cell from JavaScript code causes **the whole table to stay in memory**. Consider this carefully when keeping references to DOM elements.

当涉及到 DOM 树的内部节点或者子节点的引用时，还有一个额外的考虑因数。假设在你的 JS 代码中，有一个表格的单元格（td 标签）的引用。有一天，你决定从 DOM 在中移除这个表格，但保留着单元格的引用。凭直觉，你也许认为 GC 会垃圾回收除了这个单元格的引用外的所有东西（指表格）。实际上，这不会发生。这个单元格是表格的子节点，而子节点会保留指向父元素的引用。即，JS 代码中的表格的引用导致整个表格都驻留在内存中。

-----------------------------*Frank 注：以下为原作者的广告时间*--------------------------

We at SessionStack try to follow these best practices in writing code that handles memory allocation properly, and here’s why:

我们在 SessionStack 试图希望在写代码时遵循最佳实践来正确地处理内存的分配。原因如下：

Once you integrate SessionStack into your production web app, it starts recording everything: all DOM changes, user interactions, JavaScript exceptions, stack traces, failed network requests, debug messages, etc. 

一旦你将 SessionStack 集成到你的 web app 产品中，它会开始记录一切内容：所有的 DOM 变化， 用户交互，JS 异常，堆栈追踪，失败的网络请求，调试信息等等。

With SessionStack, you replay issues in your web apps as videos and see everything that happened to your user. And all of this has to take place with no performance impact for your web app.

有了 SessionStack, 你可以像视频中一样，重现你的 web app中的问题，并且看到你的用户的遭遇。所有这些都不会影响到你的 web app 的性能。

Since the user can reload the page or navigate your app, all observers, interceptors, variable allocations, etc. have to be handled properly, so they don’t cause any memory leaks or don’t increase the memory consumption of the web app in which we are integrated.

由于用户可以刷新页面或者导航到你的 app 的其他页面，所有的观察者，拦截器，变量分配等等必须恰当地处理。这样才不会引起内存泄露或者增加集成的 web app 内存开销。

There is a free plan so you can [give it a try now](https://www.sessionstack.com/signup).

这儿有一个免费计划，你可以[尝试一下](https://www.sessionstack.com/signup).

![img](https://cdn-images-1.medium.com/max/800/1*kEQmoMuNBDfZKNSBh0tvRA.png)

#### Resources

- [http://www-bcf.usc.edu/~dkempe/CS104/08-29.pdf](http://www-bcf.usc.edu/~dkempe/CS104/08-29.pdf)
- [https://blog.meteor.com/an-interesting-kind-of-javascript-memory-leak-8b47d2e7f156](https://blog.meteor.com/an-interesting-kind-of-javascript-memory-leak-8b47d2e7f156)
- [http://www.nodesimplified.com/2017/08/javascript-memory-management-and.html](http://www.nodesimplified.com/2017/08/javascript-memory-management-and.html)



原文链接：https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec