---
title: ReactFragment用法介绍
date: 2020-03-01 10:00:00
tags: React
---
<meta name="referrer" content="no-referrer"/>

react中一个常见模式是一个组件返回多个元素，`Fragments`允许你将子列表分组，而无需像DOM添加额外的节点。
```
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```
一个常见模式是为一个组件返回一个子元素列表。以这个示例的 React 片段为例：
```
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
```
为了渲染有效的 HTML ， `Columns`需要返回多个`td`元素。如果一个父` div` 在 `Columns` 的 `render()` 函数里面使用，那么最终的 HTML 将是无效的。
```
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```
在`Table` 组件中的输出结果如下：
```
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```
所以，我们介绍 Fragments。
```
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```
在正确的`Table`组件中，这个结果输出如下：
```
<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table>
```
如果子元素需要父元素包裹起来，但是不需要渲染父元素，我们就可以使用`fragment`。其实它的功能和vue的template一样，都只是占位，不渲染。

我们也可以把`<React.Fragment></React.Fragment>`简写为`<></>`,看着像空组件；

`<></>` 是 `<React.Fragment/>` 的语法糖。
`<></>` 语法不能接受键值或属性。

如果你需要一个带 key 的片段，你可以直接使用 `<React.Fragment /> 。
一个使用场景是映射一个集合为一个片段数组 — 例如：创建一个描述列表：
```
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 没有`key`，将会触发一个key警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```
key 是唯一可以传递给 `Fragment` 的属性。在将来，我们可能增加额外的属性支持，比如事件处理。

----
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

