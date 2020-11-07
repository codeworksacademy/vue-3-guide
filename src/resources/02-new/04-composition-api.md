# Composition API

Here there be 🐲dragons🐉 

> [Official Docs](https://v3.vuejs.org/guide/composition-api-introduction.html#introduction)

## 100% Optional!!!

> 🔨 Alright let's get one thing straight... The composition api is not a golden hammer. 

The composition api provides some nice features but remember to `KISS` if you are migrating from vue 2 to vue 3 make the minimal amount of change as possible first... Then see if the composition api can help when refactoring components...

The true benefit of the composition API from an application standpoint is being more expressive and functional in writting your components. An issue presented by Vue's Options Api is breaking the natural `this` context of standard JavaScript. Although the Options Api resolves an invalid this context there the code itself is unclear without this understanding...

Out of all the newest features in the composition API we are going to focus on the ones that will demand the most of your attention. Namely `ref`  `reactive` and `computed`

## ref

Of the three new methods this one is perhaps the most confusing and actually serves a double purpose. To over simplyfy wrapping an object in a `ref` call makes that object become an `observable` doing this allows vue 3 to tie into its updates when using the ref inside of a computed or reactive object. To get the data stored in a ref object you will have to use the `.value` property  

``` vue
<script setup>
import { computed, reactive, ref } from 'vue'

// 1. Create an observable object
const windowWidth = ref(window.innerWidth)

// 2. Native event to update the observable
window.addEventListener('resize', () => { windowWidth.value = window.innerWidth })

export const UTILS = reactive({
            // 3. whenever the observable changes the computed is triggered
  isMobile: computed(() => windowWidth.value < 600)
})
// 4. when a computed is fired in a reactive object it can cause a DOM Node to render
</script>
```

## ref vs vue 2 $ref

Here is a bit of vue magic... and as of now I am not crazy about it but perhaps in time it will change or I'll get use to it.

To avoid having a broken and abstract `this` context vue 3 always sets the `this` context to null from within your `setup` method. Removal of this means we no longer have `this.$refs.someDOMElement` so the new way of doing this is as follows

> 💡 Pay close attention to the ref name and the name of the variable they must match

``` vue
<template>
    <div>
        <h3 ref="titleElem">This is a title</h3>
    </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
    setup(){
        // set the default value since the component has not been mounted yet
        const titleElem = ref(null)

        onMounted(()=>{
            console.log(titleElem.value.textContent) // logs: This is a title
        })

        return {
            /**
             * [!] IMPORTANT: return the ref or
             *                none of this works!
            */
            titleElem
        }

    }
}
</script>
```

If you are wondering about dynamic `$refs` I would first recommend abstracting the problem to a lower component to avoid the need however it is possible and you can read about it [here](https://markus.oberlehner.net/blog/refs-and-the-vue-3-composition-api/)

## reactive

To keep this simple think of reactive as a more memory expensive version of `ref` but it works more like you would expect it to with less hassle. While there are some key differences especially in performance between `reactive` and `ref` each of them have their purpose and that is an interest best reserved to the vue docs. 

Here Ill show how we use `reactive` like the data field with the options api

> 💡 DO NOT DESTRUCTURE... it will break the reactivity

```vue
<template>
    <!-- 
    Multiple root elements 
    are called fragments and 
    work just fine in vue 3
    -->
    <div>
        {{state.person.name}}
    </div>
    <div>
        <input v-model="state.person.name">
    </div>
</template>

<script>
export default {
    setup(){
        // use a single reactive object to wrap local component data
        const state = reactive({
            person: { name: "Jimmy" }
        })

        return {
            state
        }
    }
}
</script>
```

## computed

Computed work just like they did in the options api for watching data changes. The key difference is we now see how our computed methods are wrapped in a callback and are triggered based upon the item being observed changing

computed takes in a named or anonymous function which must return a value

```vue
<script setup>
import { computed } from 'vue'
import { AppState } from '../AppState'

// Watches an external reactive object for changes: triggers DOM Render
export const profile = computed(() => AppState.profile)
</script>
```

