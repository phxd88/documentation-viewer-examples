# Getters

## Description

Getters allow to quickly get information about objects of the scene. To access getters, use the following syntax :

```javascript
this.$store.getters['viewer/getter_I_want_to_access'];
```

Some getters require parameters :

```javascript
this.$store.getters['viewer/getter_I_want_to_access'](parameter);
```

Here is a list of all the getters :
- allObjects : returns a Map with all object as value, with id as keys.
- allIds : returns all scene object ids.
- allObjectsOfType : takes a type and returns an array of all objects of this type.
- typesOf : takes an iterable of ids (Array or Set) and returns an array of all types of this ids.
- idsByTypeOf : takes an iterable of ids (Array or Set) and returns all objects ids that share types with passed object ids.
- ifcIdOf : takes an id and return the icfId to which it belongs.
- objectById : takes an id and returns the corresponding object.
- objectName : takes an id and returns the corresponding object name.
- selectedObjectIds : returns an array of all selected Ids. (the same property is present on the store state but it is a Set instead of an Array).
- structureOf : takes an id and returns the structure to which it belongs.
- objectParent : takes an id and returns the coresponding object parent.
- objectChildren : takes an id and returns the coresponding object children.
- objectDescendants : takes an id and returns the coresponding object descendants.
- objectAncestors : takes an id and returns the coresponding object ancestors.
- objectAncestorByType : takes an id and a type and returns the first ancestor with this type.
- objectStorey : takes an id and returns the first ancestor with the type "storey".
- objectSpace : takes an id and returns the first ancestor with the type "space".

## Example

Clicking the plugin icon will activate the 'select by storey' mode. You can now click on an object and all objects in the same storey will be selected.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
  <meta charset="utf-8">
  <title>BIMData - Getters - Storey</title>
  <script src="https://unpkg.com/@bimdata/viewer@0.5.0/dist/bimdata-viewer.min.js" charset="utf-8"></script>
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
    const { viewer, store, eventHub, setAccessToken } = initBIMDataViewer('app', accessToken, cfg);

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
              const viewer3D = this.$store.state.viewer.plugins.get("viewer3D");
              viewer3D.selectOnClick = !active;
              if (active) {
                document.body.style.setProperty("cursor", "crosshair", "important");
                this.pickSubscription = viewer3D.viewer.cameraControl.on(
                  "picked",
                  pickResult => {
                    if (!pickResult || !pickResult.entity) return;
                    const storey = this.$store.getters["viewer/objectStorey"](pickResult.entity.id);
                    if (storey) {
                      this.$store.state.viewer.hub.$emit("select-objects", {
                        ids: this.$store.getters["viewer/objectDescendants"](storey.uuid).map(object => object.uuid)
                      });
                    } else {
                      this.$store.state.viewer.hub.$emit("alert", {
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
