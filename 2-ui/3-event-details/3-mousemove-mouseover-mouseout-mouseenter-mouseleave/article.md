# Moving: mouseover/out, mouseenter/leave

Let's dive into more details about events that happen when mouse moves between elements.

我们详细了解一下，当鼠标在不同元素之间移动时相关的事件。

[cut]

## Mouseover/mouseout, relatedTarget

The `mouseover` event occurs when a mouse pointer comes over an element, and `mouseout` -- when it leaves.

当鼠标经过元素，`mouseover`被触发，当离开元素，`mouseout`被触发。

![](mouseover-mouseout.png)

These events are special, because they have a `relatedTarget`.

这些事件比较特殊，因为他们有`relatedTarget`属性。


For `mouseover`: 对于`mouseover`

- `event.target` -- is the element where the mouse came over.

- `event.target` -- 是鼠标要经过的元素

- `event.relatedTarget` -- is the element from which the mouse came.

- `event.relatedTarget` -- 是鼠标来自于那个元素。


For `mouseout` the reverse: 对于`mouseout`正好相反：


- `event.target` -- is the element that mouse left.

- `event.target` -- 是鼠标要离开的元素。

- `event.relatedTarget` -- is the new under-the-pointer element (that mouse left for).

- `event.relatedTarget` -- 是下一个鼠标要去的元素。


```online
In the example below each face feature is an element. When you move the mouse, you can see mouse events in the text area.

在下面的例子中，每一个脸是一个元素。当你移动鼠标时，你可以在文本框中看到事件信息。


Each event has the information about where the element came and where it came from.

每个事件的信息包括，鼠标来自哪个元素和要去那个元素。

[codetabs src="mouseoverout" height=280]
```

```warn header="`relatedTarget` can be `null`"   `relatedTarget` 可能为空 
The `relatedTarget` property can be `null`.

`relatedTarget` 属性可能为空


That's normal and just means that the mouse came not from another element, but from out of the window. Or that it left the window.


这是正常的，例如鼠标不来自任何其他的元素。但是来自浏览器之外。或者要离开浏览器。


We should keep that possibility in mind when using `event.relatedTarget` in our code. If we access `event.relatedTarget.tagName`, then there will be an error.

我们应该记住这种可能性。如果我们执行 `event.relatedTarget.tagName`，那么有可能有一个错误。


```

## Events frequency 事件频率

The `mousemove` event triggers when the mouse moves. But that doesn't mean that every pixel leads to an event.

当鼠标移动会触发`mousemove` 事件。但是不是每一像素的移动都会触发事件。


The browser checks the mouse position from time to time. And if he notices changes then triggers the events.

浏览器会时不时的检测鼠标的位置，如果有变化将触发事件。

That means that if the visitor is moving the mouse very fast then DOM-elements may be skipped:

如果用户移动鼠标非常的快，DOM元素有可能被跳过：

![](mouseover-mouseout-over-elems.png)

If the mouse moves very fast from `#FROM` to `#TO` elements as painted above, then intermediate `<div>` (or some of them) may be skipped. The `mouseout` event may trigger on `#FROM` and then immediately `mouseover` on `#TO`.

如上图，如果鼠标非常快速的从 `#FROM` 移动到 `#TO`,那么中间的 `<div>` 就会被跳过。`mouseout` 会在 `#FROM`触发，之后立即会在
`#TO`上触发 `mouseover`。

In practice that's helpful, because if there may be many intermediate elements. We don't really want to process in and out of each one.


因为如果中间有很多的元素，实际汇总就比较有用了。但是我们并不想逐个处理。



From the other side, we should keep in mind that we can't assume that the mouse slowly moves from one event to another. No, it can "jump".

从另一个方面，我们应该记住，鼠标不是一个一个的慢慢的经过元素。不是得，他可以跳过的。

In particular it's possible that the cursor jumps right inside the middle of the page from out of the window. And `relatedTarget=null`, because it came from "nowhere":

尤其是鼠标可以直接从浏览器之外直接跳到网页的中间。这样 `relatedTarget=null`，因为它不是从网页的其他部分来的。


![](mouseover-mouseout-from-outside.png)

<div style="display:none">
In case of a fast move, intermediate elements may trigger no events. But if the mouse enters the element (`mouseover`), when we're guaranteed to have `mouseout` when it leaves it.

</div>

```online
Check it out "live" on a teststand below.

在下面你的实验台中可以亲自实验一下。


The HTML is two nested `<div>` elements. If you move the mouse fast over them, then there may be no events at all, or maybe only the red div triggers events, or maybe the green one.

连个嵌套在一起的元素，如果你鼠标非常快速的经过他们，那么有可能什么是事件都没有触发，或者只触发红色 div 的时间，或者是绿色 div 的事件。

Also try to move the pointer over the red `div`, and then move it out quickly down through the green one. If the movement is fast enough then the parent element is ignored.

也尝试将鼠标移入红色 `div` ，然后快速移除红色 div，而且快速通过绿色 div。

[codetabs height=360 src="mouseoverout-fast"]
```

## "Extra" mouseout when leaving for a child 

Imagine -- a mouse pointer entered an element. The `mouseover` triggered. Then the cursor goes into a child element. The interesting fact is that `mouseout` triggers in that case. The cursor is still in the element, but we have a `mouseout` from it!

假设，一个鼠标进入一个元素。`mouseover` 被触发。然后鼠标继续进入子元素。一个有趣的现象是 `mouseout` 被触发了。但是鼠标依然在元素中。但是我们得到了一个 `mouseout`。

![](mouseover-to-child.png)

That seems strange, but can be easily explained.

似乎很奇怪，但是解释起来很简单。

**According to the browser logic, the mouse cursor may be only over a *single* element at any time -- the most nested one (and top by z-index).**

依照浏览器的规则，鼠标同一时间只能经过一个元素 -- 嵌套在最里面的元素，而且在z-index最大的元素。


So if it goes to another element (even a descendant), then it leaves the previous one. That simple.

因此如果鼠标去另一个元素（甚至是子元素），所以鼠标离开上一个元素，就是这么简单。


There's a funny consequence that we can see on the example below.
在下面的例子中，我们会看到一个有趣的结论。


The red `<div>` is nested inside the blue one. The blue `<div>` has `mouseover/out` handlers that log all events in the textarea below.
在蓝色 `div` 中包含一个红色的 `div` 。蓝色 `div` 将使用 `mouseover/out` 处理器输出相应的时间信息在 `textarea`zhong ,如下。

Try entering the blue element and then moving the mouse on the red one -- and watch the events:

尝试进入蓝色元素，然后移动到红色元素 -- 注意观察事件。

[codetabs height=360 src="mouseoverout-child"]

1. On entering the blue one -- we get `mouseover [target: blue]`.

当进入蓝色元素 -- 我们得到  `mouseover [target: blue]`

2. Then after moving from the blue to the red one -- we get `mouseout [target: blue]` (left the parent).

然后鼠标从蓝色移动红色 -- 我们得到 `mouseout [target: blue]`

3. ...And immediately `mouseover [target: red]`.

 ... 理解得到 `mouseover [target: red]` 

So, for a handler that does not take `target` into account, it looks like we left the parent in `mouseout` in `(2)` and returned back to it by `mouseover` in `(3)`.

所以，在事件处理器中，没有考虑 `target` 。好像我们在 `(2)` 的
 `mouseout` 离开父标元素，但是在 `(3)` 的 `mouseover` 有回到父元素。
 

If we perform some actions on entering/leaving the element, then we'll get a lot of extra "false" runs. For simple stuff may be unnoticeable. For complex things that may bring unwanted side-effects.

如果在鼠标进入元素和离开元素做一些动作，那么将到一些额外的错误的执行。对于简单的东西可以忽略，但是复杂的东西，可能带来一些意料之外的结果。

We can fix it by using `mouseenter/mouseleave` events instead.

然而我们可以使用 `mouseenter/mouseleave` 处理这个问题。

## Events mouseenter and mouseleave 

Events `mouseenter/mouseleave` are like `mouseover/mouseout`. They also trigger when the mouse pointer enters/leaves the element.

`mouseenter/mouseleave` 事件和 `mouseover/mouseout` 比较像。他们同样是在进入元素和离开元素时触发。


But there are two differences:

但是有两点不同：

1. Transitions inside the element are not counted.

进入元素中的元素不被考虑

2. Events `mouseenter/mouseleave` do not bubble.

`mouseenter/mouseleave`  不参与事件冒泡。

These events are intuitively very clear.

这些事件直观相当清楚。

When the pointer enters an element -- the `mouseenter` triggers, and then doesn't matter where it goes while inside the element. The `mouseleave` event only triggers when the cursor leaves it.

当鼠标进入元素 --  `mouseenter` 事件被触发，然后不不论在哪都没有关系，当在元素汇总移动。只有当 鼠标离开元素才会触发 `mouseleave`


If we make the same example, but put `mouseenter/mouseleave` on the blue `<div>`, and do the same -- we can see that events trigger only on entering and leaving the blue `<div>`. No extra events when going to the red one and back. Children are ignored.

如果我们创建同样的例子，注册 `mouseenter/mouseleave` 事件到蓝色 `div` -- 我们可以事件只在进入元素和离开元素触发。没有额外的事件，当从蓝色元素移动到红包元素，然后在回到蓝色元素。子元素完全被忽略了。

[codetabs height=340 src="mouseleave"]

## Event delegation 事件代理

Events `mouseenter/leave` are very simple and easy to use. But they do not bubble. So we can't use event delegation with them.

`mouseenter/leave` 事件简单而且易用。但是不参与事件冒泡。所以我们对他不能使用事件代理。


Imagine we want to handle mouse enter/leave for table cells. And there are hundreds of cells.

假设我们想处理表格每个单元格的 `enter/leave` 事件。而且有上百个单元格。

The natural solution would be -- to set the handler on `<table>` and process events there. But `mouseenter/leave` don't bubble. So if such event happens on `<td>`, then only a handler on that `<td>` can catch it.

最自然的方法是 -- 在 `<table>` 设置处理器，处理事件。 但是 `mouseenter/leave` 不事件冒泡。所以类似事件发生在 `<td>` 上，但是只有在  `<td>` 的事件处理器能抓取到事件。


Handlers for `mouseenter/leave` on `<table>` only trigger on entering/leaving the whole table. It's impossible to get any information about transitions inside it.

`mouseenter/leave`  事件处理器只能处理 整个表格鼠标的进入和离开事件。不能获取到任何的信息关于在 `<table>` 内部变化的信息。


Not a problem -- let's use `mouseover/mouseout`.

不是一个问题 -- 来使用  `mouseover/mouseout` 

A simple handler may look like this:

简单的处理器可以像这样：

```js
// let's highlight cells under mouse
table.onmouseover = function(event) {
  let target = event.target;
  target.style.background = 'pink';
};

table.onmouseout = function(event) {
  let target = event.target;
  target.style.background = '';
};
```

```online
[codetabs height=480 src="mouseenter-mouseleave-delegation"]
```

These handlers work when going from any element to any inside the table.

当从任何元素到任何内部元素

But we'd like to handle only transitions in and out of `<td>` as a whole. And highlight the cells as a whole. We don't want to handle transitions that happen between the children of `<td>`.

但是我们只想去处理进入和离开一个 `<td>` 事件。而且将单元格作为一个整体高亮。我们不想处理`<td>` 子元素之间的移动。

One of solutions: 

其中一个解决办法：

- Remember the currently highlighted `<td>` in a variable.

- 将当前高亮的 `<td>`  记录在一个变量中。

- On `mouseover` -- ignore the event if we're still inside the current `<td>`.

- 在 `mouseover` -- 如果我们任然在当前 `<td>` 元素中忽略他。


- On `mouseout` -- ignore if we didn't leave the current `<td>`.

- 在 `mouseout` -- 当我们还未离开当前元素，忽略它。

That filters out "extra" events when we are moving between the children of `<td>`.

当我们在 `<td>` 子元素之间移动时，过滤掉多余的事件。

```offline
The details are in the [full example](sandbox:mouseenter-mouseleave-delegation-2).
```

```online
Here's the full example with all details:

这是包含所有细节的完整实力。

[codetabs height=380 src="mouseenter-mouseleave-delegation-2"]

Try to move the cursor in and out of table cells and inside them. Fast or slow -- doesn't better. Only `<td>` as a whole is highlighted unlike the example before.

尝试移动鼠标进入和离开表格的但单元格。快慢没有关系。不像之前例子，`<td>` 元素作为一个整体被高亮了。

```


## Summary

We covered events `mouseover`, `mouseout`, `mousemove`, `mouseenter` and `mouseleave`.

我们已经介绍了 `mouseover`, `mouseout`, `mousemove`, `mouseenter` and `mouseleave`。

Things that are good to note:
最好记住下面的事：

- A fast mouse move can make `mouseover, mousemove, mouseout` to skip intermediate elements.

-  一个快速移动的鼠标使 `mouseover, mousemove, mouseout` 跳过中间的元素。


- Events `mouseover/out` and `mouseenter/leave` have an additional target: `relatedTarget`. That's the element that we are coming from/to, complementary to `target`.

 - `mouseover/out` 和 `mouseenter/leave` 事件有一个附加的target属性: `relatedTarget`.
  
  

- Events `mouseover/out` trigger even when we go from the parent element to a child element. They assume that the mouse can be only over one element at one time -- the deepest one.

- `mouseover/out` 时间即使在从父元素移动到资源也会触发。他们假设在某一个刻鼠标只能在一个元素上 -- 最深的那个。



- Events `mouseenter/leave` do not bubble and do not trigger when the mouse goes to a child element. They only track whether the mouse comes inside and outside the element as a whole.

`mouseenter/leave` 事件不能冒泡而且鼠标从父元素到资源部会触发。他们只跟踪鼠标是否进入元素和离开元素。