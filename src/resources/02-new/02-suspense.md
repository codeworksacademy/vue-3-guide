# Suspense Component

The new Suspense component is still in the experimental phase. Suspense is designed to provide a conditional template based upon an `async` process. 

Suspense pulls its inspiration from the common community `VAsync Component`

__VAsync.vue__

``` jsx
<template>
  <div style="display:contents">
    <slot name="loading" v-if="!ready && !error" />
    <slot v-else-if="results" v-bind="{results}" />
    <slot name="error" v-bind="{error}" v-else-if="error" />
  </div>
</template>
```

``` js
export default {
    props: {
        promise: {
            type: [Promise, Function],
            required: true
        }
    },
    data() {
        return {
            error: null,
            ready: false,
            results: null
        }
    },
    watch: {
        promise: {
            async handler() {
                try {
                    this.results = null
                    this.error = null
                    this.ready = false
                    this.results = await this.promise()
                    this.ready = true
                } catch (e) {
                    this.error = e.message
                }
            },
            immediate: true
        }
    }
}
```

## Using Suspense

``` jsx
<template>
  <Suspense>
    <template #fallback>
      <div>loading...</div>
    </template>
    <template #default>
      <div>{{results}}</div>
    </template>
    <template #error>
      <div>{{error}}</div>
    </template>
  </Suspense>
</template>
```
