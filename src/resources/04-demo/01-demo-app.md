# Demo Time

<center>

![small](https://bcw.blob.core.windows.net/public/downloads/5585954430080244)

<p>Alright let's get opinionated...</p>

</center>

## Naming is Hard

With years of experience using and teaching vue to various stages and talents of individuals one issue commonly arises and that is naming things...  

We all know that naming conventions can get a little wonky and something that makes pefect rational sense for one developer might sound like absolute bonkers to another. 

In an attempt to allieviate some confusion we are going to define some terms for our flavor of Vue Applications.

## Conventions

* Use Vue Components with PascalCasing names to help quickly identify components within a template.
* Vue Components should always self close unless they have a `slot` that is being used

``` jsx
<template>
  <section class="some-class">
    <header>
      <Navbar />
    </header>
    <main>
      <RouterView />
    </main>
    <footer>
      <div><img src="..." alt="..." /></div>
      <p>Blah blah blab</p>
      <SocialMediaLinks />
    </footer>
  </section>
</template>
```

## Project Structure

<pre style="color: white">
📦src
 ┣ 📂components
 ┃ ┣ 📂app                # CORE STRUCTURE 
 ┃ ┃ ┣ 📜AuthIcon.vue
 ┃ ┃ ┣ 📜Layout.vue
 ┃ ┃ ┣ 📜Navbar.vue
 ┃ ┣ 📂composable         # HEAVY USE
 ┃ ┃ ┣ 📜VBtn.vue
 ┃ ┃ ┣ 📜VDialog.vue
 ┃ ┃ ┣ 📜VInput.vue
 ┃ ┃ ┣ 📜VList.vue
 ┃ ┃ ┣ 📜VListItem.vue
 ┃ ┃ ┣ 📜VMenu.vue
 ┃ ┃ ┗ 📜VSelect.vue
 ┃ ┗ 📂video-chat         # FEATURE GROUPS
 ┃ ┃ ┗ 📜Settings.vue
 ┣ 📂pages                # NOT VIEWS
 ┃ ┣ 📜HomePage.vue
 ┃ ┗ 📜ProfilePage.vue
 ┣ 📂services             # SERVICE PATTERN
 ┃ ┣ 📜AuthService.js
 ┃ ┗ 📜Resource.js
 ┣ 📜App.vue              # ROOT NODE
 ┣ 📜AppState.js          # SINGLE SOURCE 
 ┣ 📜AuthConfig.js
 ┣ 📜main.js              # ENTRY POINT
 ┗ 📜router.js            # NO 📁/index.js
</pre>

## CORE STRUCTURE 

These are components that help to build various pages and provide a common look and feel to the application. These components should be intentionally less customizable and apperance and functionality should be predictable.

## HEAVY USE

Composable componets are designed to be used everywhere throughout the application and have a laundry list of customizations attached to them. Composable components are feature rich and center around elements that are interactive. 

> ❗💥 Do not use components as a substitute for simple css layout, Many popular libraries such as v-bootstrap and vuetify provide layout components such as `v-container`  `v-row`  `v-col`  `v-card`

Having a javascript framework handle what HTML & CSS where specifically designed for causes massive performance strain and is incredibly unecessary. Every Component added a page has to be managed by the vue stack and while Vue 3 has done a massive overhaul on DOM management to increase performance nothing can save an application from poor decision making. Use your CSS Framework of choice to manage your grid system not vue components

## FEATURE GROUPS

In an attempt to keep projects organized and each feature isolated and individually testable create directories that wrap an entire feature. Only Composable components should be used within feature sets thus limiting the dependency chain for any one feature.

## NOT VIEWS

Do not have a folder called `views` in a library called `vue` where components all end in the file extension `.vue` . In partner programming it becomes incredibly difficult to simply convey which file to open and start working on.

Pages are loaded directly by the router and never loaded into one another. Ideally pages will rely heavily on the `App Components` to provide predictable layouts with injected `Feature Components`

## SERVICE PATTERN

 `INPUTS & OUTPUTS ->`

If you dont need vue don't use it. The service pattern helps remind us not to mix our business logic directly into our front end frameworks. A large percentage of an application should be easily swapped between any client-side framework because all of the core logic can be handled without creating a dependency on the view-layer. 

## ROOT NODE

This is the top level vue element. With the small exception of the new `Teleport` component the entire application will be rendered within this node.

## SINGLE SOURCE 

All of the data for an entire application can be managed and stored in a single location. Doing this allows us to track the state of an application and take full advantage of vue's new composition api and reactive proxy objects. 

## ENTRY POINT

This is where the vue application actually starts. All plugins and extensions are now loaded directly into the app as opposed to the global vue config.

## NO 📁/index.js

A project with as many `index.js` files as it has folders is terrible to navigate. Calling files what they are is prefered as opposed to folder or directory importing... Save directory importing for external libraries.

## Global Components

While in development lazy load later. A simple trick to speed up workflow is to register all of your components globally. This can be more refined when focusing on decreasing application load size

__registerGlobalComponents.js__
```javascript
export function registerGlobalComponents(app) {
  // https://webpack.js.org/guides/dependency-management/#require-context
  // @ts-ignore
  const requireComponent = require.context('./components', true, /[a-z0-9]+\.(jsx?|vue)$/i)

  requireComponent.keys().forEach(fileName => {
    const componentConfig = requireComponent(fileName)
    const component = componentConfig.default || componentConfig
    // Prefer letting the componentName be that same as the file
    const componentName = component.name || fileName
      .substr(fileName.lastIndexOf('/') + 1)
      .replace(/\.\w+$/, '')

    // Globally register the component
    app.component(componentName, component)
  })
}

```