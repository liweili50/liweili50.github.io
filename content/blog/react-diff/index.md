---
title: React Diff 算法原理
date: "2018-12-06T23:46:37.121Z"
description: ""
---

当你使用 React，在单一时间点你可以考虑 render()函数作为创建 React 元素的树。在下一次状态或属性更新，render()函数将返回一个不同的 React 元素的树。React 需要算出如何高效更新 UI 以匹配最新的树。

有一些解决将一棵树转换为另一棵树的最小操作数算法问题的通用方案。然而，树中元素个数为 n，最先进的算法 的时间复杂度为 O(n3) 。

若我们在 React 中使用，展示 1000 个元素则需要进行 10 亿次的比较。这操作太过昂贵，相反，React 基于两点假设，实现了一个启发的 O(n)算法：

两个不同类型的元素将产生不同的树。
通过渲染器附带 key 属性，开发者可以示意哪些子元素可能是稳定的。
实践中，上述假设适用于大部分应用场景。

对比算法
当对比两棵树时，React 首先比较两个根节点。根节点的 type 不同，其行为也不同。

不同类型的元素
每当根元素有不同类型，React 将卸载旧树并重新构建新树。从`<a>`到`<img>`或从`<Article>`到`<Comment>`，或从`<Button>` 到 `<div>`，任何的调整都会导致全部重建。

当树被卸载，旧的 DOM 节点将被销毁。组件实例会调用 componentWillUnmount()。当构建一棵新树，新的 DOM 节点被插入到 DOM 中。组件实例将依次调用 componentWillMount()和 componentDidMount()。任何与旧树有关的状态都将丢弃。

这个根节点下所有的组件都将会被卸载，同时他们的状态将被销毁。 例如，以下节点对比之后：

```javascript
<div>
  <Counter />
</div>

<span>
  <Counter />
</span>
```

这将会销毁旧的 Counter 并重装新的 Counter。

相同类型的 DOM 元素
当比较两个相同类型的 React DOM 元素时，React 则会观察二者的属性，保持相同的底层 DOM 节点，并仅更新变化的属性。例如：

```javascript
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```

通过比较两个元素，React 知道仅更改底层 DOM 元素的 className。

当更新 style 时，React 同样知道仅更新变更的属性。例如：

```javascript
<div style={{color: 'red', fontWeight: 'bold'}} />

<div style={{color: 'green', fontWeight: 'bold'}} />
```

当在调整两个元素时，React 知道仅改变 color 样式而不是 fontWeight。

在处理完 DOM 元素后，React 递归其子元素。

相同类型的组件元素
当组件更新时，实例仍保持一致，以让状态能够在渲染之间保留。React 通过更新底层组件实例的 props 来产生新元素，并在底层实例上依次调用 componentWillReceiveProps() 和 componentWillUpdate() 方法。

接下来，render()方法被调用，同时对比算法会递归处理之前的结果和新的结果。

递归子节点
默认时。当递归 DOM 节点的子节点，React 仅在同一时间点递归两个子节点列表，并在有不同时产生一个变更。

例如，当在子节点末尾增加一个元素，两棵树的转换效果很好：

```javascript
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

React 将会匹配两棵树的`<li>first</li>`，并匹配两棵树的`<li>second</li>` 节点，并插入`<li>third</li>`节点树。

若原生实现，在开始插入元素会使得性能更棘手。例如，两棵树的转换效果则比较糟糕：

```
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```

React 会调整每个子节点，而非意识到可以完整保留`<li>Duke</li>` 和 `<li>Villanova</li>`子树。低效成了一个问题。

Keys
为解决该问题，React 支持了一个 key 属性。当子节点有 key 时，React 使用 key 来匹配原本树的子节点和新树的子节点。例如，增加一个 key 在之前效率不高的样例中能让树的转换变得高效：

```javascript
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

现在 React 知道带有'2014'的 key 的元素是新的，并仅移动带有'2015'和'2016'的 key 的元素。

实践中，发现 key 通常不难。你将展示的元素可能已经带有一个唯一的 ID，因此 key 可以来自于你的数据中：

```javascript
<li key={item.id}>{item.name}</li>
```

当这已不再是问题，你可以给你的数据增加一个新的 ID 属性，或根据数据的某些内容创建一个哈希值来作为 key。key 必须在其兄弟节点中是唯一的，而非全局唯一。

万不得已，你可以传递他们在数组中的索引作为 key。若元素没有重排，该方法效果不错，但重排会使得其变慢。

当索引用作 key 时，组件状态在重新排序时也会有问题。组件实例基于 key 进行更新和重用。如果 key 是索引，则 item 的顺序变化会改变 key 值。这将导致非受控组件的状态可能会以意想不到的方式混淆和更新。

这里是在 CodePen 上使用索引作为键可能导致的问题的一个例子，这里是同一个例子的更新版本，展示了如何不使用索引作为键将解决这些 reordering, sorting, 和 prepending 的问题。

权衡
牢记协调算法的实现细节非常重要。React 可能会在每次操作时渲染整个应用；而结果仍是相同的。为保证大多数场景效率能更快，我们通常提炼启发式的算法。

在目前实现中，可以表明一个事实，即子树在其兄弟节点中移动，但你无法告知其移动到哪。该算法会重渲整个子树。

由于 React 依赖于该启发式算法，若其背后的假设没得到满足，则其性能将会受到影响：

算法无法尝试匹配不同组件类型的子元素。若你发现两个输出非常相似的组件类型交替出现，你可能希望使其成为相同类型。实践中，我们并非发现这是一个问题。

Keys 应该是稳定的，可预测的，且唯一的。不稳定的 key（类似由 Math.random()生成的）将使得大量组件实例和 DOM 节点进行不必要的重建，使得性能下降并丢失子组件的状态。
