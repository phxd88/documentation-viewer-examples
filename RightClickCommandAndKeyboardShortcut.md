# Right Click Menu Command and Keyboard Shortcut

## Description

### Right Click Menu Command

To add command to the right click menu, get the right click menu plugin and register a command in created or mounted [component lifecyle method](https://vuejs.org/v2/guide/instance.html#Instance-Lifecycle-Hooks).

Warning : rightClickMenu will be undefined if you deactivate it on the viewer cfg.

```javascript
...
const rightClickMenu = this.$plugins["right-click-menu"];

rightClickMenu.registerCommand({
  label: "My custom command",     // The text that will be displayed on the right click menu
  picto: "K",                     // A character that will be displayed after the text (usually to show corresponding keyboard shortcut)
  execute: () => null,            // A function that will be executed when the command is clicked on the right click menu
  predicate: () => null           // A function that will be executed when the right click menu opens. The command will be displayed if the function returns true.
});
...
```

### Keyboard Shortcut

To register a keyboard shortcut, get the keyboardShortcutsManager and register a new shortcut.

Warning : only one shortcut must be added to a key. An error will be displayed on the console if the key is already assigned.

```javascript
this.$plugins.keyboardShortcutsManager.registerKeyboardShortcut(
  "K",                            // The key that will be assigned
  () => console.log("K pressed")  // The function that will be executed when the assigned key is pressed
);
```

## Example

The plugin we add here is not displayed to the end user. It just add a command to the right click menu and a keyboard shortcut. The command and the shortcut hide objects of type "space". The newly added right click menu command has the label "Hide spaces" (with picto K) and is only displayed when at least one "space" is visible. The shortcut is added on the key K.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
  <meta charset="utf-8" />
  <title>BIMData - Right Click Command and Keyboard Shortcut</title>
  <script src="https://unpkg.com/@bimdata/viewer@0.6.3/dist/bimdata-viewer.min.js" charset="utf-8"></script>
</head>

<body>
  <div style="height: 100vh">
    <div id="app"></div>
  </div>
  <script>
    const cfg = {
      cloudId: 88,
      projectId: 100,
      ifcIds: [175],
      bcf: false,
      reload: false,
      model: false,
      help: false,
      fullscreen: false,
      section: false,
      projection: false,
      selectOptions: false,
      structureAndProperties: false,
      bcf: false,
      logo: true,
      rightClickMenu: true, // Right click menu should be activated
      viewer3DNavCube: false
    };
    const accessToken = "DEMO_TOKEN";
    const { viewer } = initBIMDataViewer(
      "app",
      accessToken,
      cfg
    );

    viewer.registerPlugins([
      {
        name: "HideSpacePlugin",
        component: {
          render() {
            return null;
          },
          created() {
            // Add right click command
            const rightClickMenu = this.$plugins["right-click-menu"];
            const getSpaceIds = () =>
              this.$utils.getAllObjectsOfType("space").map(
                object => object.uuid
              );
            const hideSpaces = () => {
              this.$hub.emit("hide-objects", {
                ids: getSpaceIds()
              });
            };
            const isSpaceVisible = () => {
              const allObjects = Object.values(
                this.$plugins.viewer3D.viewer.scene.objects
              );
              const spaceUUIDs = getSpaceIds();
              const spaceObjects = allObjects.filter(object =>
                spaceUUIDs.includes(object.id)
              );
              return spaceObjects.some(spaceObject => spaceObject.visible);
            };
            rightClickMenu.registerCommand({
              label: "Hide spaces",
              picto: "K",
              execute: hideSpaces,
              predicate: isSpaceVisible
            });

            // Register keyboard shortcut
            this.$plugins.keyboardShortcutsManager.registerKeyboardShortcut(
              "K",
              hideSpaces
            );
          }
        },
      }
    ]);
  </script>
</body>

</html>
```
