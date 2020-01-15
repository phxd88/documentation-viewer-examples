# State

## Description

Some usefull feature are present on the viewer state.

To access the viewer state :

```javascript
this.$store.state.viewer;
```

Here is a list of these features :

```javascript
/**
 * A map containing all registered plugins with name as key and plugin as value.
 */
this.$store.state.viewer.plugins;

/**
 * The access token for BIMDataConnect.
 */
this.$store.state.viewer.accessToken;

/**
 * The client to access BIMData API. https://github.com/bimdata/javascript-api-client
 */
this.$store.state.viewer.client;

/**
 * The event hub.
 */
this.$store.state.viewer.hub;

/**
 * The keyboard shortcut manager to register shortcuts.
 */
this.$store.state.viewer.keyboardShortcutsManager;

/**
 * The modal manager to display global modals.
 */
this.$store.state.viewer.modalManager;

/**
 * A Set of all selected object ids. The same property is present on the getters but returns an Array instead.
 */
this.$store.state.viewer.selectedObjectIds;
```
