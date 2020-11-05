# Vuex

Only one small change here and the entire store is setup to take advantage of the new Composition API... Just like the App and Router we now have a method for creating a store as opposed to using the `new` keyword.

__store.js Vue 2__
```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state:{},
  mutations:{}
  actions:{},
  modules:{},
  getters:{}
})
```
__store.js Vue 3__
```js
import { createStore } from 'vuex'

export const store = createStore({
  state:{},
  mutations:{}
  actions:{},
  modules:{},
  getters:{}
})
```