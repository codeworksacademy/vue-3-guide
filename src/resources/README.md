# Getting Started

![vue3-promo](https://bcw.blob.core.windows.net/public/downloads/4724004113009141)


It's finally here Vue 3 and it is awesome! However, some of the changes might seem a bit overwhelming at first. We have put together for you a quick guide on building effective non-trivial applications with the newest features and best practices for the modern vue applicaiton. We are also tacking in identity management with Auth0 to ensure we are building secure robust applications.

This workshop will start out assuming you already have a working knowledge of JavaScript, and Vue 2 or an equivalent framework. We will be drawing direct comparisons from the framework as it used to be in Vue 2, to how it will have changed and updated for Vue 3.

This guide is intended to be used both during this event and more importantly as a guide and tutorial later on. We are going to clarify the main hurdles with diving into this framework and application toolset. This guide is not intended to replace the official docs, therefore we are not going to define topics that the official docs already cover.

Key focuses will be the Composition API and what it seeks to improve upon in comparison to the Options API. We will dive into the new Reactive objects provided to us, and how we will implement the service pattern as a replacement for Vuex. Lastly, we will discuss and implement Auth0 as an Identity as a Service platform. Auth0 facilitates managing identity, roles, and permissions through their easy to use GUI tools or directly from the server API, by providing us with a JWT, the modern go-to for secure authentication practices.

Upon completion of this workshop, attendees will have a strong understanding of Vue3 and how to integrate the Auth0 platform.

## Required Tools

We are going to use a few pre-built tools so grab these items right away.

- [Git](https://git-scm.com/)
- [Node ^14](https://nodejs.org/en/)
- [VSCode](https://code.visualstudio.com/)
  - Extensions (See Quick Install Below):
    - [Comment Anchors](https://marketplace.visualstudio.com/items?itemName=ExodiusStudios.comment-anchors)
    - [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur)
    - [Intellicode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)
    - [SCSS Formatter](https://marketplace.visualstudio.com/items?itemName=sibiraj-s.vscode-scss-formatter)
    - [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
    - [Editor Config](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)
    - [VSCode Icons](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons)
- [CodeWorks Generator](https://www.npmjs.com/package/bcw)
  - `npm i -g bcw`
- [Vue CLI](https://cli.vuejs.org/guide/installation.html)
  - `npm install -g @vue/cli`
- [Vue Dev Tools](https://chrome.google.com/webstore/detail/vuejs-devtools/ljjemllljcmogpfapbkkighbhhppjdbg)

__Quick Install__
```bash
code --install-extension sibiraj-s.vscode-scss-formatter VisualStudioExptTeam.vscodeintellicode ExodiusStudios.comment-anchors octref.vetur dbaeumer.vscode-eslint EditorConfig.EditorConfig vscode-icons-team.vscode-icons
```

__VSCode Settings__
```json
{
  "editor.suggestSelection": "first",
  "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
  "workbench.iconTheme": "vscode-icons",
  "git.enableSmartCommit": true,
  "git.confirmSync": false,
  "git.autofetch": true,
  "javascript.referencesCodeLens.enabled": true,
  "javascript.referencesCodeLens.showOnAllFunctions": true,
  "typescript.implementationsCodeLens.enabled": true,
  "typescript.referencesCodeLens.enabled": true,
  "typescript.referencesCodeLens.showOnAllFunctions": true,
  "javascript.implicitProjectConfig.checkJs": true,
  "javascript.suggest.completeFunctionCalls": true,
  "files.exclude": {
      "**/.nyc_*": true,
      "**/coverage": true,
      "**/node_modules": true
  },
  "javascript.updateImportsOnFileMove.enabled": "always",
  "explorer.confirmDragAndDrop": false,
  "vetur.format.defaultFormatter.js": "vscode-typescript",
  "eslint.alwaysShowStatus": true,
  "eslint.format.enable": true,
  "eslint.lintTask.enable": true,
  "eslint.codeActionsOnSave.mode": "all",
  "editor.codeActionsOnSave": {
      "source.fixAll": true
  },
  "[javascript]": {
      "editor.defaultFormatter": "dbaeumer.vscode-eslint"
  },
  "eslint.nodePath": "C:\\Program Files\\nodejs",
  "[vue]": {
      "editor.defaultFormatter": "dbaeumer.vscode-eslint"
  }
}
```

### Phased Release Process
![phased-release](../assets/img/phased-release.png)
> Goal: Switch all branches to Vue 3 by the end of 2020