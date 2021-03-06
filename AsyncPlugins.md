# Async plugins

## Description

If your plugin is mean to be placed on the right or leth viewer menu, you have the ability to declare it as "async":

```javascript
// plugin code
{
  ...
  asyncLoading: true,
  ...
}
```

Once your plugin is declared as async, clicking on the menu button (left or right) will trigger plugin loading. Until the correct event is emited, clicking on the menu button again will have no effect. The same behaviour occured when you deactivate your plugin (click on the menu button while plugin is displayed and loading is finished). While loading/unloading, the plugin icon will be changed by a spinner indicating that an action is in progress.

The motivation for async plugins is to prevent menu icon spam click errors. For example, while unloading a plugin, data are unload and we want to be sure that these data are correctly unloaded until the plugin open again.

The events to stop loading are "loading-end" while the plugin stop loading and "unloading-end" while the plugin finished unloading.

## Example
A simple async plugin that takes time to load/unload. While loading/unloading, clicking on the plugin button have no effect.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
  <meta charset="utf-8" />
  <title>BIMData - Async Plugin</title>
  <script src="https://unpkg.com/@bimdata/viewer@0.7.17/dist/bimdata-viewer.min.js" charset="utf-8"></script>
</head>

<body>
  <div style="height: 100vh">
    <div id="app"></div>
  </div>
  <script>
    const cfg = {
      cloudId: 88,
      projectId: 100,
      bimdataPlugins: {
        default: false
      }
    };
    const accessToken = "DEMO_TOKEN";
    const { viewer, store, eventHub, setAccessToken } = initBIMDataViewer(
      "app",
      accessToken,
      cfg
    );

    viewer.registerPlugins([
      {
        name: "asyncPlugin",
        component: {
          render() {
            return null;
          },
          props: ["active"],
          watch: {
            active(active) {
              const eventName = active ? "loading-end" : "unloading-end";
              setTimeout(() => this.$emit(eventName), 2000);
            }
          }
        },
        display: {
          iconPosition: 'left'
        },
        asyncLoading: true // THE ASYNC PLUGIN CONFIG
      }
    ]);
  </script>
</body>

</html>
```
