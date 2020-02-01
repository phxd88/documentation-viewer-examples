# Section Planes

## Description

To add section, you must first enter section mode.

```javascript
this.$store.state.viewer.hub.$emit("set-section-mode", { active: true });
```

Remember to quite section mode when you are done. (active : false).

Now you can create section planes.
- You can specify an axis and the section will be made on this axis at the center of the scene bounding box.

```javascript
this.$store.state.viewer.hub.$emit(
  "create-section-plane",
  {
    axis: "x" // "x", "y" or "z"
  }
);
```
- Or you can specify a position and a direction (both are array of length 3)
```javascript
this.$store.state.viewer.hub.$emit(
  "create-section-plane",
  {
    position: new Float32Array(3),
    direction: new Float32Array(3)
  }
);
```

To delete the displayed section plane :
```javascript
this.$store.state.viewer.hub.$emit("delete-section-plane");
```

To delete all section planes :
```javascript
this.$store.state.viewer.hub.$emit("delete-all-section-planes");
```

## Example

Clicking the plugin icon will activate the 'cutting' mode. You can now click on an object and a section plane will be added to its surface. You can add multiple section planes. To delete section planes, close the plugin.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8" />
    <title>BIMData - Section on surface</title>
    <script
      src="https://unpkg.com/@bimdata/viewer@0.4.4/dist/bimdata-viewer.min.js"
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
        rightClickMenu: false,
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
          name: "SectionOnSurfacePlugin",
          component: {
            render() {
              return null;
            },
            data() {
              return {
                pickSurfaceSubscription: null
              };
            },
            props: ["active"],
            watch: {
              active: {
                handler(active) {
                  const viewer3D = this.$store.state.viewer.plugins.get(
                    "viewer3D"
                  );
                  this.$store.state.viewer.hub.$emit("set-section-mode", {
                    active
                  });
                  viewer3D.selectOnClick = !active;
                  // viewer3D.highlightOnHover = !active; // To remove the highlight on hover
                  if (active) {
                    document.body.style.setProperty(
                      "cursor",
                      "crosshair",
                      "important"
                    );
                    this.pickSurfaceSubscription = viewer3D.viewer.cameraControl.on(
                      "pickedSurface",
                      pickResult => {
                        if (!pickResult) return;
                        this.$store.state.viewer.hub.$emit(
                          "create-section-plane",
                          {
                            position: new Float32Array(pickResult.worldPos),
                            direction: new Float32Array(pickResult.worldNormal)
                          }
                        );
                      }
                    );
                  } else {
                    document.body.style.removeProperty("cursor");
                    viewer3D.viewer.cameraControl.off(
                      this.pickSurfaceSubscription
                    );
                    this.$store.state.viewer.hub.$emit(
                      "delete-all-section-planes"
                    );
                    // this.$store.state.viewer.hub.$emit("delete-section-plane"); // To delete the active section plane
                  }
                }
              }
            }
          },
          position: "left-menu",
          keepActive: true
        }
      ]);
    </script>
  </body>
</html>
```