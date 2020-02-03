# Annotations

## Description

To add annotations on objects, simply emit the "create-annotations" event on the event hub. You must provide object ids, a priority ("low", "medium" or "hight") and an index that will be display on the annotation.

```javascript
this.$hub.emit("create-annotations", {
  ids: [/* object ids */],
  index: 0,
  priority: "low" // "low", "medium" or "high"
});
```

To delete all annotations, emit "clear-annotations" event on the event hub.

```javascript
this.$hub.emit("clear-annotations");
```

## Example

Opening the pulgin will enter the 'annotation mode'. When clicking an object, an annotation will be added on it. You must select a priority on the list in order to display an annotation. An alert will warn you that no priority is selected if you try to add an annotation without it.
Closing the plugin will delete all annotations.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
  <meta charset="utf-8">
  <title>BIMData - Annotations</title>
  <script src="https://unpkg.com/@bimdata/viewer@0.6.5/dist/bimdata-viewer.min.js" charset="utf-8"></script>
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
      rightClickMenu: false,
      viewer3DNavCube: false,
      alerts: true,
      logo: true
    }
    const accessToken = 'DEMO_TOKEN';
    const { viewer } = initBIMDataViewer('app', accessToken, cfg);

    viewer.registerPlugins([{
      name: "annotations",
      component: {
        template: `
        <div>
          <select v-model="priority" style="width: 100%;">
            <option disabled value="">Priority</option>
            <option value="low">Low</option>
            <option value="medium">Medium</option>
            <option value="high">High</option>
          </select>
          <input type="number" v-model="index" style="text-align: center;">
        </div>
        `,
        data() {
          return {
            priority: "",
            pickSubscription: null,
            index: 1
          };
        },
        props: ["active"],
        watch: {
          active: {
            handler(active) {
              const viewer3D = this.$plugins.viewer3D;
              viewer3D.selectOnClick = !active;
              // viewer3D.highlightOnHover = !active; // To remove the highlight on hover
              if (active) {
                document.body.style.setProperty("cursor", "copy", "important");
                this.pickSubscription = viewer3D.xeokit.cameraControl.on(
                  "picked",
                  pickResult => {
                    if (!this.priority) {
                      return this.$hub.emit('alert', { type: 'warning', message: 'You must select a priority.' });
                    }
                    if (!pickResult || !pickResult.entity) return;
                    this.$hub.emit("create-annotations", {
                      ids: [pickResult.entity.id],
                      index: this.index,
                      priority: this.priority
                    });
                  }
                );
              } else {
                document.body.style.removeProperty("cursor");
                viewer3D.xeokit.cameraControl.off(this.pickSubscription);
                this.index = 1;
                this.priority = "";
                this.$hub.emit("clear-annotations");
              }
            }
          }
        }
      },
      display: {
        iconPosition: "left"
      },
      keepActive: true
    }])
  </script>
</body>

</html>
```
