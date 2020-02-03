# Right Click Menu Context Commands

## Description

You can add context commands to the right click menu. Context commands are specific to the location the user right clicked.

To add context commands, your component should react on user right click :

```html
<!-- in your component template -->
...
<div @click.right="onRightClick()">
  Right click zone
</div>
...
```

Then, you will need to get the right click menu plugin and register context command.

Warning : rightClickMenu will be undefined if you deactivate it on the viewer cfg.

```javascript
const MyComponent = {
  ...
  methods: {
    onRightClick() {
      // get the right click menu plugin
      const rightClickMenu = this.$plugins.rightClickMenu;
      // register context command
      rightClickMenu.registerContextCommand({
        label: "My context command",
        execute: () => {
          console.log('Context command executed');
        }
      });
    }
  }
}
```

The command is an object with two properties :

- label: the text that will be displayed on the right click menu
- execute: a function that will be executed when the user click on the newly added right click menu command.

## Example

Open the plugin and right click on the 'Data' area. Edit command will be added to the right click context menu. Click on Edit to turn the 'Data' area into edit mode where you can change the text. Press enter to validate the new text.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8" />
    <title>BIMData - Context Menu</title>
    <script
      src="https://unpkg.com/@bimdata/viewer@0.6.5/dist/bimdata-viewer.min.js"
      charset="utf-8"
    ></script>
  </head>

  <body>
    <div style="height: 100vh">
      <div id="app"></div>
    </div>
    <script>
      const cfg = {
        cloudId: 88,
        projectId: 100,
        ifcIds: [],
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
      const { viewer, store, eventHub, setAccessToken } = initBIMDataViewer(
        "app",
        accessToken,
        cfg
      );
      viewer.registerPlugins([
        {
          name: "myCustomPlugin",
          component: {
            template: `
       <div class="context">
         <div @click.right="onRightClick()">
           <input
             ref="input"
             v-if="editMode"
             v-model="data"
             @keyup.enter="validate()"
           />
           <span v-else>{{ data || 'no data provided' }}</span>
         </div>
       </div>
       `,
            data() {
              return {
                editMode: false,
                data: "Data"
              };
            },
            methods: {
              validate(number) {
                this.editMode = false;
              },
              onRightClick(number) {
                const rightClickMenu = this.$plugins.rightClickMenu;
                if (rightClickMenu) {
                  rightClickMenu.registerContextCommand({
                    label: "Edit",
                    execute: () => {
                      this.editMode = true;
                    }
                  });
                }
              }
            }
          },
          keepActive: true,
          display: {
            iconPosition: 'left',
            content: 'simple'
          },
          tooltip: "myCustomPlugin"
        }
      ]);
    </script>
  </body>
</html>
```
