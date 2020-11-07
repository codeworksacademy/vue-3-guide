# Other Features and Tweaks

## CSS Variables

You can now pass javascript variables into your CSS style tags with the new `vars` attribute

``` vue
<template>
  <p class="js-driven-color" @click="toggleColor()">
    CLICK HERE TO CHANGE MY COLOR
  </p>
</template>

<script setup>
import { ref } from 'vue'
export const color = ref('red')
export function toggleColor() {
  color.value = color.value === 'red' ? 'blue' : 'red'
}
</script>

<style vars={color}>

.js-driven-color{
  color: var(--color);
  user-select: none;
  cursor: pointer;
}
</style>

```

## Fragments

Vue now supports multiple root elements in a template

```vue
<template>
    <div>
        🎉 I DONT HAVE A SINGLE ROOT ELEMENT
    </div>
    <div>
        BUT I STILL WORK 😁
    </div>
</template>
```

## Performance and Tree Shaking

See a side by side comparison of the same app [here](https://docs.google.com/spreadsheets/d/1VJFx-kQ4KjJmnpDXIEaig-cVAAJtpIGLZNbv3Lr4CR0/edit#gid=0)

Vue 3 is faster in everything it does and manages the entire DOM manager was rewritten and Evan explains it [here](https://www.vuemastery.com/courses/vue3-deep-dive-with-evan-you/vue3-overview/) 

**❗ The course is not free and likely wont help you in building applications but if your curious its a great watch**

On to Tree Shaking...

Tree Shaking is a process that removes any unused code from your application making your final delivery much smaller... This feature is not exactly new but it has been improved since v2.4. Tree shaking can cause some gotcha issues when thinking you will have access to something that is actually removed during the build process... These types of issues most commonly occur when you are mixing native Browser or JavaScript events with vue incorrectly... (*cough JQuery Plugins)

## TypeScript Support

The entire vue 3 was written in TypeScript so default TypeScript support has been greatly improved. Here is a great video walking through the migration of vue 2 to vue 3 typescript

[![cover](https://img.youtube.com/vi/ZWaXwO2l1tY/0.jpg)](https://www.youtube.com/watch?v=ZWaXwO2l1tY)
