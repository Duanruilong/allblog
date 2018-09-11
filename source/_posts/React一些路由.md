---
title: React一些路由
date: 2018-05-08 23:44:22
tags: [React,编程]
description: React路由Prompt、Redirect、match、Switch。
copyright: true
categories: React
top:
---
React路由Prompt、Redirect、match、Switch。

![React](https://coding.net/u/DRuilong/p/phone_drl/git/raw/master/image/react_d.png)

<!--more-->
# 1、Prompt组件 
> 除了Router、Route、Link这三个react路由的基础搭配使用，还有一些其他的比较重要的组件，比如我们在页面切换时，需要进行一些提示，我们就能使用Prompt组件。

它有一个必须的属性message，用于给用户提示信息。 
基本使用：
```hash
<Prompt message="给用户提示信息"/>
```
有时候，我们希望提示消息，有时候我们不希望提示出现，这就用到它的另外一个属性-when。when有两种情况，当它的值是true时，会提示消息。当它的值为false时，不会提示消息。 
基本使用方式：
```hash
<Prompt when={true} message="给用户提示信息"/>
```
***
# 2、Redirect组件 
> 在匹配一个路径时，但是可能这个路径，我们更希望它指向一个新的展示界面，而不是它原本的路径匹配界面。

Redirect组件的必须属性是to属性，表示重定向的新地址。 因为重定向了一个新的地址，必须有一个对应的新的地址的Route，来指定重定向的界面。
基本使用：
```hash
<Redirect to='/new-path' />
<Route path='/new-path' component={NewPage}/>
```
```hash
<Route path="/index" render={()=><Redirect to="/other"/>}/>
```
Redirect重定向是路由的重定向，应该写在组件Route中，一般使用render来实现它 
基本使用方式：
```hash
import React,{ Component } from "react";
import { render } from "react-dom";
import { BrowserRouter as Router, Route, Link, Prompt,Redirect } from "react-router-dom";

class Index extends Component{
    render(){
        return (
            <div>this a Index page</div>
        )
    }
}
class Other extends Component{
    render(){
        return (
            <div>this a Other page</div>
        )
    }
}
class Main extends Component{
    constructor(props){
        super(props);
        this.state = {
            toast: false,
        }
    }
    render(){
        return (
            <Router>
                <div>
                    <ul>
                        <li><Link to="/index">这是首页</Link></li>
                        <li><Link to="/other">这是其他页</Link></li>
                    </ul>
                    <Route path="/index" render={()=><Redirect to="/other"/>}/>
                    <Route path="/other" component={Other}/>
                </div>
            </Router>
        )
    }
}

//因为重定向，所以每个路由展示界面都是other界面

render(<Main />,document.getElementById("root"));
```
***
# 3、Switch组件
> 它的特性是我们只渲染所匹配到的第一个路由组件，一般界面渲染的时候，会渲染所有匹配到的路由组件。它的孩子节点只能是Route组件或者Redirect组件。

基本使用方式：
```hash
import { Switch } from "react-router-dom";

<Switch>
    <Route path="/" component={Test1} />
    <Route path="/Test" component={Test2} />
</Switch>
```
以下实例：
```hash
import React,{ Component } from "react";
import { render } from "react-dom";
import { BrowserRouter as Router, Route, Link, Switch } from "react-router-dom";

class Home extends Component{
    render(){
        return (
            <div>Home</div>
        )
    }
}
class Other extends Component{
    render(){
        return (
            <div>Other</div>
        )
    }
}
class Switchs extends Component{
    render(){
        return (
            <div>Switchs test</div>
        )
    }
}

class Main extends Component{
    constructor(props){
        super(props);
    }
    render(){
        return (
            <Router>
                <div>
                    <ul>
                        <li><Link to="/home">首页</Link></li>
                        <li><Link to="/other">其他页</Link></li>
                    </ul>
                    <Switch>
                        <Route path="/:id" component={Switchs}/>
                        <Route path="/home" component={Home}/>
                        <Route path="/other" component={Other}/>
                    </Switch>
                </div>
            </Router>
        )
    }
}

render(<Main />,document.getElementById("root"));
```
***
# 4、Match对象
> match是一个匹配路径参数的对象，它有一个属性params，里面的内容就是路径参数，除常用的params属性外，它还有url、path、isExact属性。

在组件Match中，通过this.props.match.params.id获取了路径的匹配参数。首页参数home，其他页是other。

Match的获取方式： 
在Route component中，组件通过this.props.match获取。 
在Route render 和Route children中，通过传递一个参数的方式获取。
```
以下实例：
```hash
import { render } from "react-dom";
import { BrowserRouter as Router, Route, Link, Prompt,Redirect } from "react-router-dom";

class Match extends Component{
    render(){
        return (
            <div>id:{this.props.match.params.id}</div>
        )
    }
}

class Main extends Component{
    constructor(props){
        super(props);
    }
    render(){
        return (
            <Router>
                <div>
                    <ul>
                        <li><Link to="/home">首页</Link></li>
                        <li><Link to="/other">其他页</Link></li>
                    </ul>

                    <Route path="/:id" component={Match}/>
                </div>
            </Router>
        )
    }
}
//id是路径匹配参数。
render(<Main />,document.getElementById("root"));
```
***