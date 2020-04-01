# Getters

## Description

Getters allow to quickly get information about objects of the scene. They are reachable using this syntax :

```javascript
this.$utils.getterIWantToAccess(someParameter);
```

Here is a list of all the getters :

```javascript
/**
 * Get all scene objects.
 * @return {Map<string, object>} a Map with all id as keys and object as values.
 */
getAllObjects();

/**
 * Get all scene object ids.
 * @return {Array<string>} all scene object ids.
 */
getAllIds();

/**
 * Get all scene object of specific type.
 * @param {string} type - a specific type.
 * @return {Array<object>} all scene object of specific type.
 */
getAllObjectsOfType(type);

/**
 * Get all types of ids.
 * @param {Array|Set<string>} ids
 * @return {Array<string>} Types of ids.
 */
getTypesOf(ids);

/**
 * Get all objects ids that share types with passed object ids.
 * @param {Array|Set<string>} ids - object ids inspected for their types.
 * @return {Array<string>} object ids with the same types as objects of ids passed in parameter.
 */
getIdsByTypeOf(ids);

/**
 * Get the icfId the object id belongs to.
 * @param {string} id - an object id.
 * @return {number} the icfId the object id belongs to.
 */
getIfcIdOf(id);

/**
 * Get object by id.
 * @param {string} id - an object id.
 * @return {object} an object with this id.
 */
getObject(id);

/**
 * Get object name.
 * @param {string} id - an object id.
 * @return {string} the object name.
 */
getObjectName(id);

/**
 * Get all selected Ids. (the same property is present on the store state but it is a Set instead of an Array).
 * @return {Array<string>} selected object ids.
 */
getSelectedObjectIds();

/**
 * Get the structure of an object id.
 * @param {string} id - an object id.
 * @return {object} the corresponding structure.
 */
getStructureOf(id);

/**
 * Get object parent.
 * @param {string} id - an object id.
 * @return {object} the parent of the object.
 */
getObjectParent(id);

/**
 * Get object children.
 * @param {string} id - an object id.
 * @return {object[]} the children of the object.
 */
getObjectChildren();

/**
 * Get object descendants.
 * @param {string} id - an object id.
 * @return {object[]} the descendants of the object.
 */
getObjectDescendants();

/**
 * Get object ancestors.
 * @param {string} id - an object id.
 * @return {object[]} the descendants of the object.
 */
getObjectAncestors();

/**
 * Get the first ancestor of an object with a specific type.
 * @param {string} id - an object id.
 * @param {string} type - an object type.
 * @return {object} the first ancestor of the object with a specific type.
 */
getObjectAncestorByType(id, type);

/**
 * Get the first ancestor of an object with the type "storey".
 * @param {string} id - an object id.
 * @return {object} the first ancestor of the object with the type "storey".
 */
getObjectStorey(id);

/**
 * Get the first ancestor of an object with the type "space".
 * @param {string} id - an object id.
 * @return {object} the first ancestor of the object with the type "space".
 */
getObjectSpace(id);
```

## Example

Clicking the plugin icon will activate the 'select by storey' mode. You can now click on an object and all objects in the same storey will be selected.
The hover is storey dependent.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8" />
    <title>BIMData - Getters - Storey</title>
    <script
      src="https://unpkg.com/@bimdata/viewer@0.7.17/dist/bimdata-viewer.min.js"
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
        bimdataPlugins: {
          default: false,
          rightClickMenu: true,
          alerts: true
        }
      };
      const accessToken = "DEMO_TOKEN";
      const { viewer } = initBIMDataViewer(
        "app",
        accessToken,
        cfg
      );

      viewer.registerPlugins([
        {
          name: "storey-select",
          component: {
            render() {
              return null;
            },
            data() {
              return {
                pickedNothingSubscription: null,
                pickSubscription: null,
                hoverSubscription: null,
                hoverOffSubscription: null,
                highlightedStorey: null
              };
            },
            props: ["active"],
            methods: {
              deselectAll() {
                this.$hub.emit("deselect-objects", {
                  ids: this.$utils.getSelectedObjectIds()
                });
              },
              unhighlightAll() {
                if (this.highlightedStorey) {
                  this.$hub.emit("unhighlight-objects", {
                    ids: this.$utils
                      .getObjectDescendants(this.highlightedStorey.uuid)
                      .map(object => object.uuid)
                  });
                  this.highlightedStorey = null;
                }
              }
            },
            watch: {
              active: {
                handler(active) {
                  const viewer3D = this.$plugins.viewer3D;
                  viewer3D.selectOnClick = !active;
                  viewer3D.highlightOnHover = !active;
                  if (active) {
                    document.body.style.setProperty(
                      "cursor",
                      "crosshair",
                      "important"
                    );
                    this.pickSubscription = viewer3D.xeokit.cameraControl.on(
                      "picked",
                      pickResult => {
                        if (!pickResult || !pickResult.entity) return;
                        const storey = this.$utils.getObjectStorey(
                          pickResult.entity.id
                        );
                        if (storey) {
                          this.$hub.emit("select-objects", {
                            ids: this.$utils
                              .getObjectDescendants(storey.uuid)
                              .map(object => object.uuid)
                          });
                        } else {
                          this.$hub.emit("alert", {
                            type: "infos",
                            message: "No storey found for clicked object."
                          });
                        }
                      }
                    );

                    this.pickedNothingSubscription = viewer3D.xeokit.cameraControl.on(
                      "pickedNothing",
                      this.deselectAll
                    );

                    this.hoverSubscription = viewer3D.xeokit.cameraControl.on(
                      "hover",
                      hit => {
                        if (hit && hit.entity && hit.entity.isObject) {
                          const storey = this.$utils.getObjectStorey(
                            hit.entity.id
                          );
                          if (storey) {
                            if (
                              !this.highlightedStorey ||
                              storey !== this.highlightedStorey
                            ) {
                              if (
                                this.highlightedStorey &&
                                storey !== this.highlightedStorey
                              ) {
                                this.unhighlightAll();
                              }
                              this.$hub.emit("highlight-objects", {
                                ids: this.$utils
                                  .getObjectDescendants(storey.uuid)
                                  .map(object => object.uuid)
                              });
                              this.highlightedStorey = storey;
                            }
                          }
                        }
                      }
                    );
                    // Deselect all on hover off
                    this.hoverOffSubscription = viewer3D.xeokit.cameraControl.on(
                      "hoverOff",
                      this.unhighlightAll
                    );
                  } else {
                    this.unhighlightAll();
                    document.body.style.removeProperty("cursor");
                    viewer3D.xeokit.cameraControl.off(
                      this.pickedNothingSubscription
                    );
                    viewer3D.xeokit.cameraControl.off(this.pickSubscription);
                    viewer3D.xeokit.cameraControl.off(this.hoverSubscription);
                    viewer3D.xeokit.cameraControl.off(
                      this.hoverOffSubscription
                    );
                  }
                }
              }
            }
          },
          display: {
            iconPosition: "right"
          },
          keepActive: true
        }
      ]);
    </script>
  </body>
</html>
```
