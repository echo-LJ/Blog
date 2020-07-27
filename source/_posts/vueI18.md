---
title: vue项目中引入&使用国际化
date: 2020-07-27 17:24:07
tags:
---

* 国际化思路：将当前语言代码-国家地区对照表（zh-CN）放到cookie中和vuex中,每次通过读取cookie&vuex中的语言代码，来展示对应的语言包，在切换语言的时候将语言代码存入到cookie&vuex中（保证刷新页面后能记住用户选择的语言）。
####引入vue-i18n&vuex&js-cookie
vue-i18n是vue国际化依赖包,vuex是状态管理。
```
npm install --save vue-i18n vuex js-cookie
```
在main.js中注册引入即可。
```
import VueI18n from 'vue-i18n'
import Cookies from 'js-cookie'
Vue.use(VueI18n)
import store from 'vuex'
new Vue({
  el: '#app',
  i18n,
  store,
  components: { App },
  template: '<App/>'
}
```
小编是将其放在store&lang之中,在main.js中引入两个文件
```
import store from './store'
import i18n from './lang'
// 由于这里用到了element组件库，这里需要的同学自行安装element-ui
// npm install element-ui
//  Vue.use(Element, {
//  i18n: (key, value) => i18n.t(key, value)
// })
new Vue({
  el: '#app',
  router,
  i18n,
  store,
  components: { App },
  template: '<App/>'
})
```
lang文件夹：当前以中英文为例，需要支持多个语言则可添加同级语言包。
```
lang
  |--index.js   //主文件
  |
  |-- zh.js      //中文语言包
  |
  |-- zh.js      //英文语言包

```
```
`index.js`
import Vue from 'vue'
import VueI18n from 'vue-i18n'
import Cookies from 'js-cookie'
import elementEnLocale from 'element-ui/lib/locale/lang/en' // element-ui lang
import elementZhLocale from 'element-ui/lib/locale/lang/zh-CN'// element-ui lang
import enLocale from './en'
import zhLocale from './zh'

Vue.use(VueI18n)

const messages = {
  en-US: {
    ...enLocale,
    ...elementEnLocale
  },
  zh-CN: {
    ...zhLocale,
    ...elementZhLocale
  }
}

const i18n = new VueI18n({
  locale: Cookies.get('language') || 'en-US',
  messages
})

export default i18n

```
```
`zh.js`
  export default {
  login: {
    title: '欢迎'
    logIn: '登录'
 }
`en.js`
export default {
  login: {
    title: 'Welcome',
    logIn: 'Log in'
 }
```
使用实例：
```
<span>{{$t('login.title')}}</span>
// 在语言代码为中文（zh-CN）时,则展示为：欢迎
// 在语言代码为英文（en-US）时,则展示为：Welcome'
```
store文件夹：vue&cookie中存入语言代码。
```
store
  |--index.js   //主文件
  |
  |-- modules      //vuex子模块文件
          |
          |-- language.js      //语言
```
```
`index.js`
import Vue from 'vue'
import Vuex from 'vuex'
import language from './modules/language'

Vue.use(Vuex)

const store = new Vuex.Store({
  modules: {
    language
  }
})

export default store
```
```
`language.js  `
import Cookies from 'js-cookie'
import Cookies from 'js-cookie'

const app = {
  state: {
    language: Cookies.get('language') || 'en-US' // 项目初始化时，默认为英文
  },
  mutations: {
    SET_LANGUAGE: (state, language) => {
      state.language = language
      Cookies.set('language', language)
    },
  },
  getters: {
    language: state => state.language
  },
  actions: {
    setLanguage({ commit }, language) {
      commit('SET_LANGUAGE', language)
    },
  }
}

export default app
```
在项目需要切换语言的位置，添加如下事件即可，（事件：将语言代码存入到vuex中，vuex中调用setcookie即可存入到cookie中,并改变i18n中的local即可切换语言。）
```
handleSetLanguage(lang) {
  this.$i18n.locale = lang
  this.$store.dispatch('setLanguage', lang)
}
```
以上即可实现国际化！！！！！
😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊😊




