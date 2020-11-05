# Create App

## Main.js

Vue 3 has made some substantial changes to its overall architecture and this is reflected in many new functions designed for creating the various components of a vue application. There are quite a few new methods to learn but we will go over those in a later section.

__main.js-Vue 2__
```js
import Vue from 'vue'
import App from './App.vue'

new Vue({
  data: {
    message: 'Vue 2 simple setup',
  },
  render: h => h(App)
  /*...various plugins...*/
}).$mount('#app');

```
__main.js-Vue 3__
```js
import { createApp } from 'vue'
import App from './App.vue'

const rootApp = createApp(App)
rootApp
    .use(router)
    .use(store)
    .mount('#app')
```

## Why CreateApp

Vue 3 steps away from a problem known as prototype polution there are several reasons for doing this but we will focus on the breaking changes this causes. Many libraries and vue plugins would extend the vue prototype and allow you to create global variables accessible to all of your components. This feature has specifically been disabled so there are a lot of plugins right now that will not work straight across with vue 3. A big player here is Vuetify and sadly it is not currently compatible with Vue 3. Changes and updates are in the works with a potential release in January