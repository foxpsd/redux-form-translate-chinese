# props

## 参数介绍

### anyTouched:boolean
当 form 中至少有一个 field 被标记为 touched，该值就为 true。
###array : Object
 一组在表单中被默认预先绑定的 action 生成器。你可以利用它们来操作 array fields(`<FieldArray>`组件)。array 中包含以下 action 生成器，所有这些生成器都不返回内容。所有这些生成器的第一个参数都是目标 array field 的 name

- array.insert(field:String, index:Number, value:Any) : Function
>在你表单的 array field 的 目标索引位置中插入一个值。
- array.move(field:String, from:Number, to:Number) : Function
>移动 array field 中的一个值，把他从 from 所在的索引位置移动到 to 所在的索引位置。其他值自动前移或者后移。
- array.pop(field:String) : Function
>从 array field 的尾部弹出一个值（相当于删除）。
- array.push(field:String, value:Any) : Function
>在 array field 的尾部压入一个值。
- array.remove(field:String, index:Number) : Function
>在 array field 中的规定索引位置处删除一个值。
- array.removeAll(field:String) : Function
>删除 array field 中的所有值。
- array.shift(field:String) : Function
>从 array field 的首部弹出一个值（相当于删除）。
- array.splice(field:String, index:Number, removeNum:Number, value:Any) : Function
>对一个 array field 执行数组的 splice 方法（集删除和插入为一体，具体请查看js array 的 splice 方法）。
- array.swap(field:String, indexA:Number, indexB:Number) : Function
>交换 array filed 中两个索引位置的值。
- array.unshift(field:String, value:Any) : Function
>在 array field 的首部插入一个值

### asyncValidate : Function
一个可能被调用的，用于初始化异步验证的方法
### asyncValidating : String | boolean
它的值可能为：
- *false*：当前没有在执行异步验证
- *true*：异步验证过程正在进行中
- *某字符串*： 触发执行异步验证的某个 field 的 name
### autofill(field:String, value:any) : Function
设置一个 field 的值，并在 redux store 中将这个 field 的 autofilled 属性标记为 ture。这对于某些需要由程序控制设置值的 field 来说很管用，但是最好通过某种方式（比如用 autofilled 的值来标志某种样式）来使用户知道，这个值是程序控制设置的，而不是用户自己设置的。
### blur(field:String, value:any) : Function
修改一个 field 的值并标志其 blur 属性为 true
### change(field:String, value:any) : Function
修改一个 field 的值
### destroy() : Function
销毁 redux store 中表单的 state 。默认中这个方法会在 componentWillUnmount 的生命周期中被调用。
### dirty : boolean
当表单中至少有一个值与初始值不同时，该变量即标志为true。
### error : any
当同步验证、异步验证，或者传入 onSubmit 的 promise 被拒绝时，其输出的_error就会生成该对象。该对象用于回显验证结果。
### form : String
这是你传给 reduxForm() 装饰器的表单名，或者是在外部传给你自定义表单组件的 prop 值。
### handleSubmit(eventOrSubmit) : Function

这个方法应该被放置于 `<form onSubmit={handleSubmit}>` 或者是 `<button onClick={handleSubmit}>`.它将负责运行同步和异步的表单验证，并且，如果验证通过，它将收集表单内容，并调用 `this.props.onSubmit(data)` 方法来提交表单。
你可以选择性地把你自己的 onSubmit 方法当做参数传递给 handleSubmit 方法，以替换默认的 onSubmit 属性。例子： `<form onSubmit={handleSubmit(this.save.bind(this))}>`。

如果你自己的 onSubmit 方法返回一个 promise，那么 submitting 标志将会被设置为true ，直到 promise 成功返回或被拒绝。如果 promise 被拒绝，并返回了一个提交错误对象，比如 `new SubmissionError({ field1: 'error', field2: 'error' }) `，那么接下来，这个提交错误对象会被加到每个 field 的 error 属性中。如果错误没有和任何一个具体的 field 关联，而是指向整个表单，那么你将传递这个错误并假设它和一个虚拟的，名为 _error 的 field 关联，并且它会被赋给这个 field 的 error 变量。

重述要点，有两个使用 handleSubmit 的方法
1. 传递给它一个方法，让它去调用
```
<button onClick={handleSubmit(data => {
    // do something with data. validation will have been called at this point,
    // so you know the data is valid
})}>Submit</button>
```
2. 在你的自定义表单组件中传入这么一个方法，赋给 onSubmit 属性。
```
<MyDecoratedForm onSubmit={data => {
    // do something with data. validation will have been called at this point,
    // so you know the data is valid
}}/>
```
### initialize(data:Object) : Function
用给的值来初始化表单数据。dirty 标志和 pristine 标志的值都会由表单当前值与传给 initialize 方法的初始值做比较来得到。
### initialValues : Object
和传给 reduxForm 装饰器的 initialValues 初始值相同。
### invalid : boolean
如果表单验证出现错误，这个标志值就为 true，和 valid 值相反。
### pristine: boolean
如果表单中至少有一个 field 的值和初始值不同，就为 true，和 dirty 相反。
###reset() : Function
把当前表单的值重新设为初始值，并把 pristine 标志值重新设为 true。
### submitFailed : boolean
初始值是 false，当 onSubmit 绑定方法被调用，并且因为错误(验证未通过等)而提交失败，这个标志值就会被设为 true。接下来的成功提交会重新把这个值设为 fasle
### submitSucceeded : boolean
初始值是 false，当 onSubmit 绑定方法被调用，并且成功提交，这个标志值就会被设为 true 。接下来的提交失败会重新把这个值设为 false。
### submitting : boolean
无论当前你的表单在不在提交，这个属性都只会当你的 onSubmit 方法返回一个 promise 时起效。这个值一直会是 ture，直到 promise 返回成功或者被拒绝。
### touch(...field:string) : Function
把一系列指定的 field 标记为 touched ,使其能够回显错误。
### untouch(...field:string) : Function
清除一系列指定 field 的"touched"标志值。
### valid : boolean
当表单通过验证（没有返回error）时，这个值为 true。