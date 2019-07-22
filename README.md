## 在web端如何实现路由切换的动画效果

> 注意: 该修改仅对web端生效. 实现方式参考vue中\<router-view\> 的动画添加
https://router.vuejs.org/zh/guide/advanced/transitions.html

> weex本身限制并不支持\<transition\>标签, 而小程序naviate自带切换动画, 无需做额外修改

### 安装cli

> npm i chameleon-tool@0.4.0-alpha.2 -g

### 文件修改

修改目录: src/app/app.cml

为app添加transition标签
```html
<template>
  <transition name="{{slide}}">
    <app store="{{store}}" router-config="{{routerConfig}}"></app>
  </transition>
</template>
```
根据路由的 from.path 和 to.path 动态的修改切换样式
```JavaScript
class App {
  data = {
    store,
    routerConfig,
    slide: 'slide-to-left'
  }
  created(res) {
  }
  methods = {
    changeSlideToRight() {
      this.slide = 'slide-to-right'
    },
    changeSlideToLeft() {
      this.slide = 'slide-to-left'
    }
  }
  watch = {
    '$route'(to, from) {
      // 根据路由, 动态调整页面的切入动画, 可以根据需求自定义切换效果
      if (to.path == '/cml/h5/index' && from.path == '/cml/h5/navigatePage') {
        this.changeSlideToRight()
      } else {
        this.changeSlideToLeft()
      }
    }
  }
}
```

```css
/* 只在web端添加动画的样式, 在其他端不生效 */
@media cml-type(web) {
  /* 
  指定跳转下一页的动画为从左向右切入
  */
  .slide-to-left-enter {
    transform: translateX(100%);
  }
  .slide-to-left-enter-to {
    transform: translateX(0);
  }
  .slide-to-left-enter-active {
    transition: all .3s;
  }
  /* 
  指定回到下一页的动画为从右向左切入
  */
  .slide-to-right-enter {
    transform: translateX(-100%);
  }
  .slide-to-right-enter-to {
    transform: translateX(0);
  }
  .slide-to-right-enter-active {
    transition: all .3s;
  }
}

```
