#	ES5与ES6写法对照表
####导语：
>初学React/React Native 的时候， 大家都特别容易被ES6或ES5的语法所被迷惑，网上搜到的很多教程，语法也不一致，导致我么在初学的时候照猫画虎都不知道怎么去做去。 今天在此，整理了一个ES5和ES6的写法对照表， 希望大家以后读代码时能够辨认， 并且按照ES6的写法开发自己的应用。

####一、模块
#####引用
在ES5里， 如果使用CommonJS标准， 引入React包基本通过require进行，代码如下：

```js
//ES5
var React = require("react");
var {
	Component,
	PropTypes
} = Reac;  //引用React抽象组件

var ReactNative = require("react-native");
var {
	Image,
	Text,
} from ReactNatvie; //引入具体的ReactNative组件 
```
在ES6里， import写法更为标准：

```js
//ES6
import React, { 
	Component, 
	PropTypes 
} from 'react';
import {
	Image,
	View,
	Text
} from 'react-native';
```
注意在ReactNative中， import直到0.12+版本，才能正常运作。

#####导出单个类
在ES5里， 要导出一个类给别动模块用， 一般通过module.exports来导出， 代码如下：

```js
//ES5
var MyComponent = React.createClass({
	...
});
module.exports = MyComponent
```
在ES6里， 通常用export default 来实现相同的功能：

```js
export default class MyComponent extends Component {
	...
}
```
引用的时候也类似：

```js
//ES5
var MyComponent = require('./MyComponent');
//ES6
import MyComponent from './MyComponent';
```

####二、组件
#####定义组件
在ES5里， 通常通过React.createClass来定义一个组件类，像这样：

```js
//ES5
var Photo = React.createClass({
	render: function() {
		return (
			<Image source={this.props.source} />
 		);
	},
});
```
在ES6里， 我们通过定一个一个继承自React.Component的class来定一个一个组件：

```js
//ES6
class Photo extends React.Component {
	render() {
		return (
			<Image source={this.props.source} />
		);
	}
}
```
#####给组件定义方法
从上面的例子里，可以看到， 给组件定义方法，不再用 <font color="red">名字： function()</font>的写法， 而是直接用<font color="red">名字()</font>,在方法的最后，也不能有豆号了。

```js
//ES5
var Photo = React.createClass({
	componentWillMount: function() {
		...
	},
	render: function() {
		return (
			<Image source={this.props.source} />
		);
	},
});
```
```js
//ES6
class Photo extends React.Component {
	componentWillMount() {
		...
	} //注意，不再有逗号
	render() {
		return (
			<Image source={this.props.source} />
		);
	}
}
```

#####定义组件的属性类型和默认类型
在ES5里， 属性类型和默认属性分别通过propTypes成员和getDefaultProps方法来实现

```js
//ES5
var Video = React.createClass({
	getDefaultProps: function() {
		return {
			autoPlay: false,
			maxLoops: 10,
		};
	},
	propTypes: {
		autoPlay: React.PropTypes.bool.isRequired,
		maxLoops: React.PropTypes.number.isRequired,
		videoSrc: React.PropTypes.string.isRequired,
	},
	render: function() {
		return (
			<View />
		);
	},
})
```
在ES6里， 可以统一使用static成员来实现

```js
//ES6
class Video extends React.Component {
	static defaultProps = {
		autoPlay: false,
		maxLoops: 10,
	}; //注意这里有分号
	static propTypes = {
		autoPlay: React.PropTypes.bool.isRequired,
		maxLoops: React.PropTypes.number.isRequired,
		videoSrc: React.PropTypes.string.isRequired,
	}; //注意这里是分号
	render() {
		return (
			<View />
		);
	} //注意，这里即没有分号，也没有逗号
}
```

也有人这么写，不怎么推荐， 但是读到代码的时候应该明白它的意思。

```js
//ES6
class Video extends React.Component {
	render() {
		render (
			<View />
		);
	}
}
Video.defaultProps = {
	autoPlay: false,
	maxLoops: 10,
};
Video.propTypes = {
	autoPlay: React.PropTypes.bool.isRequired,
	maxLoops: React.PropTypes.number.isRequired,
	videoSrc: React.PropTypes.string.isRequired,
};
```
注意： 对React开发者而言， static成员在IE10及之前的版本不能被继承， 而在IE11和其他浏览器上可以， 这有些时候会带来一些问题， 而对ReactNative而言， 不用考虑这麻烦的事了。

####初始化state
ES5下情况类似，代码如下：

```js
//ES5
var Video = React.createClass({
	getInitialState: function() {
		return {
			loopsRemaining: this.props.maxLoops,
		};
	},
})
```
ES6下，有两种写法：

```js
//ES6
class Video extends React.Component {
	state = {
		loopsRemaining: this.props.maxLoops,
	}
}
```
不过我们推荐更易理解的在构造函数中初始化（这样，可以根据需要做一些计算）：

```js
//ES6
class Video extends React.Component {
	constructor(props) {
		super(props);
		this.state = {
			loopsRemaining: this.props.maxLoops,
		};
	}
}
```

####三、把方法作为回调提供
很多习惯于ES6的用户反而不理解在ES5下可以这么做：

```js
//ES5
var PostInfo = React.createClass({
	handleOptionsButtonClick: function(e) {
		//Here, this refers to the component instance.
		this.setState({
			showOptionsModal: true
		});
	},
	render: function() {
		return (
			<TouchableHighlight onPress={this.handleOptionsButtonClick}>
				<Text>{this.props.label}</Text>
			</TouchableHighlight>
		)
	},
});
```
在ES5下， React.createClass会把所有的方法都bind一遍， 这样，可以提交到任意的地方作为回调函数， 而this不会变化。但是官方现在逐步认为这反而逝不标准，不被理解。

在ES6下，你需要通过bind来绑定this引用， 或者使用箭头函数（他会绑定当前scope的this引用）来调用

```js
class PostInfo extends React.Component {
	handleOptionsButtonClick(e) {
		this.setState({
			showOptionsModal: true
		});
	}
	render() {
		return (
			<TouchableHighlight
				onPress={this.handleOptionsButtonClick.bind(this)}
				onPress={e=>this.handleOptionsButtonClick(e)}
			>
				<Text>{this.props.label}</Text>
			</TouchableHightlight>
		)
	}
}
```
箭头函数实际上是在这里定义了一个临时的函数， 箭头函数的箭头 => 之前是一个空括号、单个的参数名、或用括号括起来的多个参数名， 而箭头之后可以是一个表达式（作为函数的返回值）, 或者是用花括号括起来的函数体（需要自行通过return来返回值， 否则返回的是undefined）。

```js
//箭头函数的例子
() => 1
v => v+1
(a, b) => a+b
() => {
	alert("foo");
}
e => {
	if (e == 0) {
		return 0;
	}
	return 1000/e;
}
```
需要注意的是， 不论是bind还是箭头函数， 每次被执行都返回的是一个新的函数引用， 因此如果你还需要函数的引用去做一些别动事情（譬如写卸载监听器）， 那么你必须自己保存这个引用

```js
//错误的做法
class PauseMenu extends React.Component {
	componentWillMount() {
		AppStateIOS.addEventListener('change', this.onAppPaused.bind(this));
	}
	componentDidUnmount() {
		AppStateIOS.removeEventListener('change', this.onAppPaused.bind(this));
	}
	onAppPaused(event) {
		...
	}
}
```
```js
//正确的做法
class PauseMenu extends React.Component {
	constructor(props) {
		super(props);
		this._onAppPaused = this.onAppPaused.bind(this);
	}
	componentWillMount() {
		AppStateIOS.addEventListener('change', this._onAppPaused);
	}
	componentDidUnmount() {
		AppStateIOS.removeEventListener('change', this._onAppPaused);
	}
	onAppPaused(event) {
		...
	}
}

```
从这个<a href="http://www.tuicool.com/articles/Rj6RFnm">帖子</a>中，我们还学习到一种新的做法：

```js
//正确的做法
class PauseMenu extends React.Component {
	componentWillMount() {
		AppStateIOS.addEventListener('change', this.onAppPaused);
	}
	componentDidUnmount() {
		AppStateIOS.removeEventListener('change', this.onAppPaused);
	}
	onAppPaused = (event) => {
		//把方法直接作为一个arrow function的属性来定义， 初始化的时候就绑定好了。
	}
}

```

####四、Mixins
在ES5下，经常会使用mixin来为我们的类添加一些新的方法， 譬如PureRenderMixin

```js
var PureRenderMixin = require('react-addons-pure-render-mixin')
React.createClass({
	mixins: [PureRenderMixin],
	render: function() {
		return <div className={this.props.className}>foo</div>
	}
});
```

####五、ES6+带来的其他好处
#####解构&属性延展
结合使用ES6+的解构和属性延展， 我们给调用者传递一批属性更为方便了， 这个例子把className以外的所有属性传递给div标签：

```js
class AutoloadingPostsGrid extends React.Component {
	render() {
		var {
			className,
			...others, //contains all properties of this.props except for className
		} = this.props;
		return (
			<div className={className}>
				<PostsGrid {...others} />
			</div>
		);
	}
}
```
下面这种写法， 则是传递所有属性的同时， 用覆盖新的className:

```js
<div {...this.props} className="override">
	...
</div>
```
这个例子则相反， 如果属性中没有包含className， 则提供默认的值， 而如果属性中已经包含了， 则使用属性中的值

```js
<div className="base" {...this.props}>
	...
</div>
```
总的意思就是后者属性覆盖前者属性。






