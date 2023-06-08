该demo主要是复现一个在React16版本下搜索功能可能存在的bug，本项目选择了16.14.0版本

项目中点击搜索输入内容，会出现空指针的错误，使用chatGPT4查询问题原因如下：

> 提问：在前端领域，使用React框架，用 input 标签  触发 onChange 事件，是产生的event，在异步状态中获取是否会存在异常

> 回答：在React中，事件对象（例如由 onChange 生成的事件）被称为"合成事件"（Synthetic Events），它们被设计成在事件的回调函数执行结束后立即被清除以节省内存。这样，如果你在异步状态（例如，setTimeout或Promise）中访问事件对象，可能会遇到问题，因为事件可能已经被清除并且属性无法访问。
> 
> 为了解决这个问题，React提供了 event.persist() 方法，该方法允许你保留事件的属性以在异步状态中访问。例如：

```javascript
function handleChange(event) {
  event.persist();
  setTimeout(() => {
    console.log(event.target.value);
  }, 0);
}
```

> 这段代码首先调用了 event.persist() 来从事件池中删除该事件对象，然后在setTimeout的回调函数中访问事件的属性。
> 
> 但是，需要注意的是，虽然这样可以解决问题，但是频繁使用 event.persist() 可能会影响性能，因为这将阻止事件对象被有效地重复使用。在某些情况下，更好的解决方案可能是立即读取你需要的属性，并将其存储在变量中，然后在异步状态中使用这个变量。例如：


```javascript
function handleChange(event) {
  const value = event.target.value;
  setTimeout(() => {
    console.log(value);
  }, 0);
}

```

> 这段代码立即从事件对象中提取所需的属性（在这种情况下是 target.value），并将其存储在局部变量 value 中，然后在setTimeout的回调函数中使用这个变量。这样，即使事件对象被清除，你仍然可以访问所需的数据。
