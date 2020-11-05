# Component Data

This is a change that has been suggested by the core vue team for a while and vue 3 will make it no longer backwards compatable. Fortunately the change is simple all component data properties must now be a method that returns an object. 

__component.vue vue2__
```js
export default {
    data: {
        /*...*/
    }
}
```
__component.vue vue3__
```js
export default {
    data(){
        return {
            /*...*/
        }
    }
}
```

> Composition API Example will be covered later

__component.vue vue3-composition__
```js
import { reactive } from 'vue'
export default {
    setup(){
        const data = reactive({
            /*...*/
        })
        return data
    }
}
```