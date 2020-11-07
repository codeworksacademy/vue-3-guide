# AppState 

Reactivity without vuex... just use the reactive wrapper

``` javascript
import {
    reactive,
    computed
} from 'vue'

export const AppState = reactive({
    user: {},
    profile: {},
    /** @type Bug[] */ // Use JSDocs or TypeScript for intellisense
    bugs: []
})

// NOTE Getters are just computeds extracted to a higher scope...
export const Getters = {
    closedBugs: computed(() => bugs.filter(b => b.closed)),
    openBugs: computed(() => bugs.filter(b => !b.closed)),
}
```
