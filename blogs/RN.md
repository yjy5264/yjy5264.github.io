# 总结一下今年的react native 填坑之旅，已双端上线

一套代码双端上线，互联网产品，页面100左右，复杂逻辑页面、复杂交互组件若干，接入相册，音频，地图，分享，第三方支付。

### ES6
1. () => {} 箭头函数在RN里面尤为重要，扮演着bind(this)的作用，从此就可以告别闭包了。
2. promise在写法上没有async/await简洁好用，async/await直接把异步写出了同步的感觉。
3. … 这样语法糖也是简化了太多的代码。
4. util和api可以用static关键字

### ScrollView
1. 可以用onScroll来监听滑动的距离等参数。
2. 可以用horizontal配合pagingEnabled来实现横屏轮播图。
3. 有一个scrollTo方法可以控制滑动位置。
4. 当我想做一个更复杂的跨平台组件的时候发现好像缺个东西，滑动结束，于是我在源码里发现了这两个onScrollEndDrag，onMomentumScrollEnd，于是就写出了日历，下拉刷新，选择器，侧滑按钮等跨平台组件。选择器组件地址https://github.com/yjy5264/react-native-roll-picker
5. pagingEnabled只能实现一个ScrollView宽度的分页，所以我用两个ScrollView重叠结合动画实现了卡片样式的轮播图，轮播图组件地址https://github.com/yjy5264/react-native-card-carousel

### Navigator
1. 用跳转时判断当前路由是否等于即将跳转路由可以解决重复跳转的问题。
2. 使用popToRoute()而不是_popN()来实现返回N个路由，从而避免网络延迟点击多次造成闪退的问题。

### Text
1. 不同的字体大小对应Text的上下空白高度不同，而且安卓和iOS还不一样，对于像我这种强迫症患者，还是推荐使用flex来定义Text的布局。

### WebView
1. 安卓某些低版本不支持注入JS代码，所以在WebView高度自适应的问题上采用的是网站加载结束返回网站高度给URL(#height)，WebView监听URL变化并得到height

### 动画与手势
1. 结合动画与手势可以做到手势跟随到效果，抽屉组件地址https://github.com/yjy5264/react-native-pan-drawer
2. RN的手势好像并不支持多点触控，我测试的时候同时间只有一个点有效，所以没有用RN实现图片缩放的功能。

### 原生组件
1. 接入了高德地图，QQ微博微信分享，还有支付宝支付这些需要分别写iOS和android的原生SDK。
2. 相册这块使用的react-native-image-picker。