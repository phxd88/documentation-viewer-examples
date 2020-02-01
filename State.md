# State

## Description

Some usefull feature are present using the '$' syntax.

Here is a list of these features :

```javascript
/**
 * An object containing all registered plugins.
 */
this.$plugins.pluginIWantToAccess;

/**
 * The access token for BIMDataConnect.
 */
this.$utils.getAccessToken();

/**
 * The client to access BIMData API. https://github.com/bimdata/javascript-api-client
 */
this.$bimdataApiClient;

/**
 * The event hub.
 */
this.$hub;

/**
 * The keyboard shortcut manager to register shortcuts.
 */
this.$plugins.keyboardShortcutsManager;

/**
 * The modal manager to display global modals.
 */
this.$plugins.modalManager;

/**
 * A Set of all selected object ids. The same property is present on the getters but returns an Array instead.
 */
this.$utils.getSelectedObjectIds();
```
