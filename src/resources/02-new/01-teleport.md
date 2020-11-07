# Teleport Component

![teleporting](https://bcw.blob.core.windows.net/public/downloads/7800874194844651)

Teleport was designed to facilitate moving content and components out of their inner node tree with ease... This is a poweful new feature of vue and mirrors almost exactly what react calls `portals` 

Although react uses portals this component should not be confused with HTML's new native [`portal`](https://web.dev/hands-on-portals/) element. Likely react will end up changing the portal component name due to this conflict.

## Common Teleport Use Case
- Modals
  - Resolves all the odd z-idex and clickjacking issues
- Toast Notifications
  - Same reason as modals
- ToolTips
- Floating Menus
- Games???

## Teleport Example
[Source](https://github.com/codeworksacademy/v3-demos/blob/main/src/pages/PortalsDemo.vue)