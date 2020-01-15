# Events

## Description

You can emit custom events using the hub :

```javascript
this.$store.state.viewer.hub.$emit("my-event", payload);
```

You can listen to events the same way :

```javascript
this.$store.state.viewer.hub.$on("my-event", callback);
```

You can use some vuex utilities to simplify the getters access :

```javascript
import { createNamespacedHelpers } from "vuex";
const { mapState } = createNamespacedHelpers("viewer");

// Your Vuejs component
export default {
  computed: {
    ...mapState(["hub"])
  },
  methods: {
    myMethod() {
      // the access is simplified
      this.hub.$emit("my-event");
    }
  }
};
```

The viewer default plugins are using existing events that you can emit or listen :

```javascript
/**
 * Set the projection type
 * @param {string} projection - the projection type "perspective" or "ortho".
 */
hub.$emit("set-projection-type", { projection });

/**
 * Set section mode.
 * @param {boolean} active - true if section mode is active.
 */
hub.$emit("set-section-mode", { active });

/**
 * Create a section plane.
 * options can be either axis or direction AND position.
 * @param {string} options.axis - an axis on wich to create section plane. Accepted values : "x", "y" or "z".
 * @param {Float32Array(3)} options.direction
 * @param {Float32Array(3)} options.position
 */
hub.$emit("create-section-plane", options);

/**
 * Delete the active section plane.
 */
hub.$emit("delete-section-plane");

/**
 * Delete all section planes.
 */
hub.$emit("delete-all-section-planes");

/**
 * Select object ids.
 * @param {Array|Set<string>} ids - the ids of objects to select.
 */
hub.$emit("select-objects", { ids });

/**
 * Deselect object ids.
 * @param {Array|Set<string>} ids - the ids of objects to deselect.
 */
hub.$emit("deselect-objects", { ids });

/**
 * Show objects.
 * @param {Array|Set<string>} ids - the ids of objects to show.
 */
hub.$emit("show-objects", { ids });

/**
 * Hide objects.
 * @param {Array|Set<string>} ids - the ids of objects to hide.
 */
hub.$emit("hide-objects", { ids });

/**
 * Highlight objects.
 * @param {Array|Set<string>} ids - the ids of objects to highlight.
 */
hub.$emit("highlight-objects", { ids });

/**
 * Unhighlight objects.
 * @param {Array|Set<string>} ids - the ids of objects to unhighlight.
 */
hub.$emit("unhighlight-objects", { ids });

/**
 * Colorize objects.
 * @param {Array|Set<string>} ids - the ids of objects to colorize.
 * @param {Array(3)} color - the color to apply on objects.
 */
hub.$emit("colorize-objects", { ids, color });

/**
 * Set viewpoint.
 * @param {object} viewpoint - the viewpoint to set (https://xeokit.github.io/xeokit-sdk/docs/class/src/plugins/BCFViewpointsPlugin/BCFViewpointsPlugin.js~BCFViewpointsPlugin.html)
 */
hub.$emit("set-viewpoint", viewpoint);

/**
 * Fit view on objects.
 * @param {Array|Set<string>} ids - the ids of objects to fit the view.
 */
hub.$emit("fit-view-objects", { ids });

/**
 * Isolate objects.
 * @param {Array|Set<string>} ids - the ids of objects to isolate.
 */
hub.$emit("isolate-objects", { ids });

/**
 * Unisolate all objects.
 */
hub.$emit("unisolate-all-objects");

/**
 * Create annotations.
 * @param {Array|Set<string>} ids - the ids of objects on wich to create annotation.
 * @param {number|string} index - the index that will be displayed on annotations.
 * @param {string} priority - the priority that will change the annotation aspect. Values accepted : "low", "medium", or "hight"
 */
hub.$emit("create-annotations", { ids, index, priority });

/**
 * Delete all annotations.
 */
hub.$emit("clear-annotations");
```

## Example

Example description

```html

```
