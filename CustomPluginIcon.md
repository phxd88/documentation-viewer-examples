# Custom Plugin Icon

## Description

When a plugin is displayed on the left or right viewer menu, the icon can be customized. It is possible to use svg path or image uri (should be a valid uri that will be put in src attributs of an [img element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img), with svg format, base64 ...).

### Direct svg path :

```javascript
// plugin code
...
icon: {
  svg: {
    path: `<circle cx="250" cy="250" r="210" fill="#fff" stroke="#000" stroke-width="50"/>`,
    options: {
      x: 500,
      y: 500
    }
  }
},
...
```

The path have to be put in the path property, and options can have width, height, x and y properties. (x and y are the last components of the svg viewbox attributs).

### Image uri :

```javascript
// plugin code
...
icon: {
  imgUri: "a valid image uri"
}
...
```

Finally, plugin will have two states in menus. Activated or not. Another icon could be displayed when activated if specified in the iconActive property (the options are the same as on icon property).


## Example

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
  <meta charset="utf-8" />
  <title>BIMData - Custom Icon</title>
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

    const bimdataIconUri = 'data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" id="Calque_1" x="0px" y="0px" viewBox="-25 0 145 145" style="enable-background:new 0 0 145 145;" xml:space="preserve"><g><polygon xmlns="http://www.w3.org/2000/svg" points="56.47,31.2 56.47,37.7 77.34,24.38 77.34,138.36 35.39,138.36 35.39,128.96 29.92,128.96 29.92,143.83   82.82,143.83 82.82,14.39 " /><polygon xmlns="http://www.w3.org/2000/svg" points="13.38,28.8 13.38,58.69 18.85,55.2 18.85,55.2 18.85,32.57 18.85,31.8 51,11.29 51,32.57 51,34.69   51,34.69 56.47,31.2 56.47,1.3 " /><path xmlns="http://www.w3.org/2000/svg" d="M4.45,64.39v64.57h52.03V31.2L4.45,64.39z M51,123.48H9.92V67.39L51,41.19V123.48z" /></g></svg>';

    viewer.registerPlugins([
      {
        name: "asyncPlugin",
        component: {
          render() {
            return null;
          },
        },
        display: {
          iconPosition: 'left'
        },
        icon: {
          svg: {
            path: `<circle cx="250" cy="250" r="210" fill="#fff" stroke="#000" stroke-width="50"/>`,
            options: {
              x: 500,
              y: 500
            }
          }
        },
        iconActive: {
          imgUri: bimdataIconUri
        }
      }
    ]);
  </script>
</body>

</html>
```
