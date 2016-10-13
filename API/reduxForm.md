# reduxForm(config:Object)

## 加载
```
var reduxForm = require('redux-form').reduxForm;  // ES5
import { reduxForm } from 'redux-form';  // ES6
```

## 配置属性
*重要：所有这些配置项都有可能在“设计时”传入`reduxForm()`方法，或者在运行时传给自定义表单组件*

### 必须
#### form : String [required]
表单的名字，在 redux-form 自带的 reducer 装配 state 时，会将每个表单的 state 数据装配到以这个名字作为 key 的对象上。

### 可选
#### asyncBlurFields : Array<String> [optional]
一个存储一些 field 的 name 的数组。这些指定的 field 的 onBlur 事件绑定的方法，会在异步验证执行的时候被调用。默认为空数组。
查看 [异步验证的例子](http://redux-form.com/6.0.5/examples/asyncValidation/) 以获取更多细节。
#### asyncValidate : (values:Object, dispatch:Function, props:Object, blurredField:String) => Promise<undefined, errors:Object> [optional]
这个方法获取所有的表单值、dispatch 方法、传给组件的 props 属性，以及当前处于 blur 状态的 field，然后返回一个处理验证的 Promise，当验证失败时需要返回一个 error 对象，例如`{ field1: <String>, field2: <String> }`
查看 [异步验证的例子](http://redux-form.com/6.0.5/examples/asyncValidation/) 以获取更多细节。
#### destroyOnUnmount : boolean [optional]
决定当你的自定义组件处于unmounted状态时，form 的 state 是否需要被销毁。
#### enableReinitialize : boolean [optional]
当这个值为 true 的时候，表单会随着 initialValues 的变化而重新初始化。默认为否。如果 keepDirtyOnReinitialize 选项也被设置了，那么当重新初始化时，状态为 dirty 的field将会保持不变。
#### getFormState : Function [optional]
这个方法获取整个 Redux state 并且返回一个state片段 redux-form reducer 会根据这个 state 片段的结构来装配实际中的 state。这个功能很少用到，默认状态下 reducer 会根据表单的 key（name）来装配 state。
#### keepDirtyOnReinitialize : boolean [optional]
当这个值被设置为 true， 同时 enableReinitialize 也被设置了，那么表单中处于 dirty 状态的 field 的值将不会随着重新初始化而改变。当这个值没有被设置时（默认），重新初始化会替换所有 field 中的值。当表单存在动态更新，或者要在提交后依然可以正常修改的场合，这个设置项是很有用的。它能防止重新初始化覆盖用户修改。默认为 false。
#### initialValues : Object<String, String> [optional]
在 componentWillMount() 生命周期中用于初始化表单的数据。这个值应该符合下面的结构：`{ field1: 'value1', field2: 'value2' }`
#### onSubmit : Function [optional]
当表单组件中的 handleSubmit() 方法被触发时，这个方法就会被调用，你必须把它作为一个参数传给你表单组件中的 handleSubmit() 方法。
如果你自己的 onSubmit 方法返回一个 promise，那么 submitting 标志将会被设置为true ，直到 promise 成功返回或被拒绝。如果 promise 被拒绝，并返回了一个提交错误对象，比如 `new SubmissionError({ field1: 'error', field2: 'error' }) `，那么接下来，这个提交错误对象会被加到每个 field 的 error 属性中。如果错误没有和任何一个具体的 field 关联，而是指向整个表单，那么你将传递这个错误并假设它和一个虚拟的，名为 _error 的 field 关联，并且它会被赋给这个 field 的 error 变量。
#### onSubmitFail : Function [optional]
这是一个回调函数，当无论什么原因导致表单提交失败时就会被调用，它接受失败对象 error 作为参数。
#### dispatch : Function
Redux 本身的 dispatch 方法
#### onSubmitSuccess : Function [optional]
这是一个回调函数，当表单提交成功时就会被调用。它接受两个参数，第一个参数是请求结果对象 result。第二个参数是 redux 自带的 dispatch 方法
#### propNamespace : String [optional]
如果被指定了，那么所有被传入你的自定义表单组件的 prop 都会被在由这个属性为名的命名空间下。如果你使用了其他装饰插件，那么这个属性能够很有效地避免属性在同一个命名空间下导致的冲突。
#### pure : boolean [optional]
如果被设置为 true，那么 shouldComponentUpdate 操作只会比较和 Redux 连接的属性，这会避免不必要的更新。想象一个组件是“pure”的，除了它自己的 props 和选中的 Redux store 中的 state，它不依赖其他任何输入和 state
这个属性和 react-redux 的 connect() API 中的 pure 参数很像。
#### shouldAsyncValidate(params) : boolean [optional]
这是一个可选的函数，便于你去完全控制异步验证过程的发生。这个函数有以下参数

- asyncErrors : Object [optional]
>所有存在的异步验证错误。
- initialized : boolean [required]
>如果表单被任何值初始化过，这个值就为 true。
- trigger : String [required]
>指明触发异步验证的事件，可以为'blur'或'submit'。这分别取决于是否一个 field 的 blur 事件触发了异步验证，或者是提交操作触发了异步验证。
- blurredField : string [optional]
>触发了异步验证的 field 的 name。可能为 undefined。
- pristine : boolean [required]
>和表单的 pristine 一致。
- syncValidationPasses : boolean [required]
>当同步验证通过时就为 true，当同步验证失败时就为 false。

默认的处理流程像下面那样
```
  if(!syncValidationPasses) {
    // 如果同步验证都没通过就不必进行异步验证了
    return false
  }
  switch(trigger) {
    case 'blur':
      // blurring
      return true
    case 'submit':
      // submitting,只有表单是 dirty 的且没有初始化过，才进行异步验证
      return !pristine || !initialized
    default:
      return false
  }
```
#### touchOnBlur : boolean [optional]
当 field 的 blur 事件触发时，是否要将 field 设为 touched，默认为 true。
#### touchOnChange : boolean [optional]
当 field 的 change 事件触发时，是否要将 field 设为 touched，默认为 false。
#### persistentSubmitErrors : boolean [optional]
当 field 的 change 事件触发时，是否要将其 error 对象移除，默认为 false。
#### validate : (values:Object, props:Object) => errors:Object [optional]
同步验证方法。获取所有的表单值和传入组件的 props。如果验证通过，它应该返回一个空对象。如果验证失败，它应该返回一个如下格式的错误对象。
`{ field1: <String>, field2: <String> }`
这个方法默认为：
`(values, props) => ({})`
查看 [同步验证例子](http://redux-form.com/6.0.5/examples/syncValidation/) 来获取更多细节。

## Instance API

*下面是你能在自定义组件的实例中获取的方法和属性*

#### dirty : boolean
当前表单的值中至少有一个与初始值不同时，即为 true，否则为 false。
true when the current form values are different from the initialValues, false otherwise.
#### invalid : boolean
当前表单是无效的时（存在表单验证错误），即为 true，否则为 false。
true when the form is invalid (has validation errors), false otherwise.
#### pristine : boolean
和 dirty 相反。
#### registeredFields : Array
一个存放对象的数组，对象包括所有 field 的名字和类型，主要用于测试。
#### reset() : void
让表单回到初始状态，pristine 标志值会被设为ture。
#### submit() : Promise
提交表单的方法。返回一个 promise。
#### valid : boolean
和 valid 相反。
#### values : Object
表单中所有 field 的当前值
#### wrappedInstance : ReactElement
被 reduxForm() 方法包装的组件的实例的引用。主要用于测试。
