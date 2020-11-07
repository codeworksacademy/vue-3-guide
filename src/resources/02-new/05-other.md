# Other Features and Tweaks

## CSS Variables

You can now pass javascript variables into your CSS style tags with the new `vars` attribute

``` vue
<template>
    <p class="js-driven-color" @click="toggleColor">
        CLICK HERE TO CHANGE MY COLOR
    </p>
</template>

<script setup>
export let color = 'red'
export function toggleColor(){
    color = color === 'red' ? 'blue' : 'red'
}
</script>

<style vars={color}>

.js-driven-color{
    color: var(--color)
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

