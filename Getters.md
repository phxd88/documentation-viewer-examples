# Getters

## Description

Getters allow to quickly get information about objects of the scene. They are reachable using this syntax :

```javascript
this.$utils.getterIWantToAccess(someParameter);
```

Here is a list of all the getters :
- getAllObjects() : returns a Map with all object as value, with id as keys.
- getAllIds() : returns all scene object ids.
- getAllObjectsOfType(type) : takes a type and returns an array of all objects of this type.
- getTypesOf(ids) : takes an iterable of ids (Array or Set) and returns an array of all types of this ids.
- getIdsByTypeOf(ids) : takes an iterable of ids (Array or Set) and returns all objects ids that share types with passed object ids.
- getIfcIdOf(id) : takes an id and return the icfId to which it belongs.
- getObject(id) : takes an id and returns the corresponding object.
- getObjectName(id) : takes an id and returns the corresponding object name.
- getSelectedObjectIds() : returns an array of all selected Ids.
- getStructureOf(id) : takes an id and returns the structure to which it belongs.
- getObjectParent(id) : takes an id and returns the coresponding object parent.
- getObjectChildren(id) : takes an id and returns the coresponding object children.
- getObjectDescendants(id) : takes an id and returns the coresponding object descendants.
- getObjectAncestors(id) : takes an id and returns the coresponding object ancestors.
- getObjectAncestorByType(id, type) : takes an id and a type and returns the first ancestor with this type.
- getObjectStorey(id) : takes an id and returns the first ancestor with the type "storey".
- getObjectSpace(id) : takes an id and returns the first ancestor with the type "space".

## Example

Clicking the plugin icon will activate the 'select by storey' mode. You can now click on an object and all objects in the same storey will be selected.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
  <meta charset="utf-8">
  <title>BIMData - Getters - Storey</title>
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
      rightClickMenu: true,
      viewer3DNavCube: false,
      alerts: true,
      logo: true
    }
    const accessToken = 'DEMO_TOKEN';
    const { viewer } = initBIMDataViewer('app', accessToken, cfg);

    viewer.registerPlugins([{
      name: "storey-select",
      component: {
        render() {
          return null;
        },
        data() {
          return {
            pickSubscription: null
          };
        },
        props: ["active"],
        watch: {
          active: {
            handler(active) {
              const viewer3D = this.$plugins.viewer3D;
              viewer3D.selectOnClick = !active;
              if (active) {
                document.body.style.setProperty("cursor", "crosshair", "important");
                this.pickSubscription = viewer3D.viewer.cameraControl.on(
                  "picked",
                  pickResult => {
                    if (!pickResult || !pickResult.entity) return;
                    const storey = this.$utils.getObjectStorey(pickResult.entity.id);
                    if (storey) {
                      this.$hub.emit("select-objects", {
                        ids: this.$utils.getObjectDescendants(storey.uuid).map(object => object.uuid)
                      });
                    } else {
                      this.$hub.emit("alert", {
                        type: "infos",
                        message: "No storey found for clicked object."
                      });
                    }
                  }
                );
              } else {
                document.body.style.removeProperty("cursor");
                viewer3D.viewer.cameraControl.off(this.pickSubscription);
              }
            }
          }
        }
      },
      display: {
        iconPosition: "right"
      },
      keepActive: true
    }])
  </script>
</body>

</html>
```
