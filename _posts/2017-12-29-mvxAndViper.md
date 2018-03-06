---
layout:     post
title:      "iOS中的架构模式"
subtitle:   "——浅谈 MVC, MVP, MVVM 和 VIPER"
date:       2017-12-29 12:00:00
author:     "Marco Liu"
header-img: "img/post-bg-2015.jpg"
catalog:    true
keywords: "iOS,架构模式,MVC,MVP,MVVM,VIPER"
description: "iOS开发中的架构模式，浅谈 MVC, MVP, MVVM 和 VIPER"
tags:
    - IOS开发
---

> “Hope for the best. ”


## 前言

做iOS开发也有一段时间了，最近闲暇之余总结了一下iOS中的几种架构模式，架构模式没有教科书式的标准答案，关键在于架构师和团队成员达成一致认识，然后在遇到问题时不断的调整，跨越瓶颈。

每个人对架构和设计模式都有不同的理解，在此记录下我的一些小见解，仅供参考，欢迎批评指正。

## 正文

###### 架构模式

如果不关心架构，想象某天你调试一个巨大的类，里面有着数十个不同关联的东西，你会发现几乎不可能定位问题点并修复bug。当然，你也很难去随心所欲地使用这个类，因为不了解类其中的一些重要细节。

但是程序的复杂度并不会因哪种酷炫的架构模式所减少，能做到的只是对复杂度的切分和控制，即：

* 1. 让一大坨恶心的代码变成几小坨不那么恶心的代码。
* 2. 让恶心的代码只在一个地方恶心。

---
### MVC

在传统的`MVC`结构中，数据层在发生改变之后会通知视图层进行对应的处理，视图层能直接访问数据层。但在iOS中，`M`和`V`之间禁止通信，必须由`C`控制器层来协调`M`和`V`之间的变化。如下图所示，`C`对`M`和`V`的访问是不受限的，但`M`和`V`不允许直接接触控制器层，而是由多种`Callbacks`方式来通知控制器
![](/img/in-post/mvc/mvc01.png)

###### MVC的分层

三个部分按照其各自的职责划分：

* **数据Model**： 负责封装数据、存储和处理数据运算等工作
* **视图View**： 负责数据展示、监听用户触摸等工作
* **控制器Controller**： 负责业务逻辑、网络请求、事件响应、数据加工等工作

###### MVCS

现有的大多数应用都会将网路服务组织成单独的一层，所以有时候你会看到所谓的 MVCS 架构模式，它其实只是在 MVC 的基础上加上了一个服务层（Service），而在 iOS 中常见的 MVC 架构模式也都可以理解为 MVCS 的形式，当引入了 Service 层之后，整个数据的获取以及处理的流程是这样的：

* 1、Controller发起网络请求
* 2、Service返回开箱即用的Model交还给 Controller 控制器
* 3、最后由 View 层展示服务端返回的数据；

###### 违背 MVC 的日常代码

```swift
var cell = tableView.dequeueReusableCellWithIdentifier("identifier") as UserCell
cell.configureWithModel(model)
```

这个 cell（View）直接调用了 Model，实际上已经违背了 MVC 模型的规则，但这种情况很普遍，以至于人们不觉得这里有什么不对。如果严格遵守 MVC 的话，要把对 cell 的配置放在 Controller 中，而不是向 cell（View） 传递一个 Model 对象。当然这会让 Controller 的体积变得更庞大。

---
### MVP

MVP 作为一个比较有争议的架构模式，在维基百科的 Model-view-presenter 词条中被描述为 MVC 设计模式的变种（derivation），个人觉得其实就是MVC。
![](/img/in-post/mvc/mvp01.jpg)
在 MVP 中，UIViewController 的子类实际上是 View 而不是 Presenter。这个定义让结构更加清晰，代价是开发速度的降低。因为必须手动进行数据和事件绑定。

我们知道实际上 MVC 中的 View 和 Controller 是紧密耦合的。MVP 中的中间者 Presenter 与 ViewController 的生命周期、页面布局没有任何关系，它负责更新 View 的数据与状态。


###### MVP实例

```swift
import UIKit
 
struct Person { // Model
    let firstName: String
    let lastName: String
}
 
protocol GreetingView: class {
    func setGreeting(greeting: String)
}
 
protocol GreetingViewPresenter {
    init(view: GreetingView, person: Person)
    func showGreeting()
}
 
class GreetingPresenter : GreetingViewPresenter {
    unowned let view: GreetingView
    let person: Person
    required init(view: GreetingView, person: Person) {
        self.view = view
        self.person = person
    }
    func showGreeting() {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.view.setGreeting(greeting)
    }
}
 
class GreetingViewController : UIViewController, GreetingView {
    var presenter: GreetingViewPresenter!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
 
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
 
    func didTapButton(button: UIButton) {
        self.presenter.showGreeting()
    }
 
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
 
    // 布局代码
}
//  MVP
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
let presenter = GreetingPresenter(view: view, person: model)
view.presenter = presenter
```

---
### MVVM




---
### VIPER



---
### 知识储备


#### 1.DISPATCH_DECL







