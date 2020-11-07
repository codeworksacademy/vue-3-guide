# Transition Changes

This one is simple though I don't have any good explanation for why the core team decided to make this change... I find it simply frustrating... 

Transitions work off of simple CSS classes and follow a paticular three part naming convention. The Vue `Transition` component and router handle adding and removing the appropriate classes so there is not much to change here.

``` css
.basename-action-motion
```

## Transition v-enter to v-enter-*from*

__transitions.scss vue-2__

``` css
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to {
    opacity: 0;
}
```

__transitions.scss vue-3__

``` css
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}
```

## Transition Use

``` jsx
<Transition name="fade">
    <div v-if="someControl"></div>
</Transition>
```

## Router Transitions

Due to some changes with the router we can no longer wrap `RouterView` in a `Transition` component. This syntax has change as below. 

__router transtion vue 2__

``` vue
<template>
  <transition name="route" mode="out-in">
    <router-view />
  </transition>
</template>
```

__router transtion vue 3__

``` vue
<template>
  <router-view v-slot="{Component}">
    <transition name="route" mode="out-in">
      <component :is="Component" />
    </transition>
  </router-view>
</template>
```
