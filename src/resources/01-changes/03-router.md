# Vue Router

Similar to vue's `createApp` the router is now created with a couple of built in methods. These changes were implemented to improve the router various modes and targeting platforms.

__router.js vue-2__
```js
import VueRouter from 'vue-router'

export const router = new VueRouter({
    mode: 'history',
    routes: [
      { path: '/', name: 'Home', component: Home },
      { path: '/about', name: 'About', component: About },
      { path: '*', name: 'redirect', redirect: '/' }
    ],
  })
```
__router.js vue-3__
```js
import { createRouter, createWebHashHistory } from 'vue-router'
// alternative history options available
export const router = createRouter({
    mode: createWebHashHistory(),
    routes: [
      { path: '/', component: Home },
      { path: '/about', component: About },
      { path: '/:catchAll(.*)*', name: 'NotFound', component: NotFound404 }
      // { path: '/:catchAll(.*)*', name: 'redirect', redirect: '/', component: null}
    ],
  })
```