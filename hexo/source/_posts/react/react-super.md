---
title: REACT 传递参数的方式
---

# 开始

> 阅读官方文档，发现react有多种传递参数的方式，下面我们就来梳理一下~

<!-- more -->

## 状态提升

> 这应该是我们最熟悉的传递参数的方式，应用场景为如果多个组件用到同一组数据，那么我们最好就将这组数据提升到他们共同的父组件里面去管理，比如我们有个组件A，他需要显示某人的姓名，如下

``` js
    function A(props) {
        var usrs = [
            {name: '张三', age: 20},
            {name: '李四', age: 22},
        ]
        return usrs.map((usr) => {
            return <p>{usr.name}</p>;
        })
    }
```

> 组件B显示某人的年龄

``` js
    function B(props) {
        var usrs = [
            {name: '张三', age: 20},
            {name: '李四', age: 22},
        ]
        return usrs.map((usr) => {
            return <p>{usr.age}</p>;
        })
    }

    function C(){
        return (
            <div>
                <A />
                <B />
            </div>
        )
    }
    ReactDOM.render(
    <C />,
    document.getElementById('root')
    )
```

> 如果这组数据是相同的一组数据，这种情况我们就需要状态提升到他们的父组件C中去，即：

``` js
    function C(){
    var usrs = [
        {name: '张三', age: 20},
        {name: '李四', age: 22},
    ]
    var names = usrs.map(usr => usr.name);
    var ages = usrs.map(usr => usr.age);
    console.log(names, ages)
    return (
        <div>
        <A names={names}/>
        <B ages={ages}/>
        </div>
        )
    }
    function A(props) {
    return props.names.map((name) => {
        return <p>{name}</p>;
    })
    }
    function B(props) {
    return props.ages.map((age) => {
        return <p>{age}</p>;
    })
    }

    ReactDOM.render(
    <C />,
    document.getElementById('root')
    )
```

> 这样如果A想改变B的数据，就不用调用C在改变B了，直接改C就行，会简单很多

## Context

> 这个是为了共享全局的数据，比如主题，或者用户名等数据,首先要创建一个Context

``` js
const theme1 = React.createContext("dark");
function C() {
  // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
  // 无论多深，任何组件都能读取这个值。
  // 在这个例子中，我们将 light 作为当前的值传递下去。
  return (<theme1.Provider value="light">
    <B />
  </theme1.Provider>)
}
class B extends React.Component {
  //这里必须使用 contextType才能使context有值
  static contextType = theme1;
   render (){
   return <span>{this.context}</span>
  } 
}
```

> Context会使组件复用性变差，并依赖于外部参数，需要谨慎使用

## 组合组件

> 如果你只是想避免层层传递一些属性，组件组合有时候是一个比 context 更好的解决方案
若一组数据只需要最深一层组件使用，中间组件不使用，那么我们可以把最深的组件整个传递下去，而不是传递每个数据

``` js
function Page(props) {
  const user = props.user;
  const content = <Feed user={user} />;
  const topBar = (
    <NavigationBar>
      <Link href={user.permalink}>
        <Avatar user={user} size={props.avatarSize} />
      </Link>
    </NavigationBar>
  );
  return (
    <PageLayout
      topBar={topBar}
      content={content}
    />
  );
}
```