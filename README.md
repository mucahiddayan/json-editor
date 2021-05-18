# json-editor

This is a custom web component, which provides you an easy editing ability of json.

### Usage:

in HTML:

```HTML
<json-editor></json-editor>
```

in main.js:

```javascript
// after dom is ready
const jsoneditor = document.querySelector("json-editor");
const json = { a: 12, b: 13, c: { d: 14 }, e: [15, 16, 17] };

// pass json to an editor
jsoneditor.value = json;

// update json object on update of any field
jsoneditor.$on("update", ({ detail }) => {
  // we can use lodash.set to update json object
  // detail.path is the path to field which is currenly updated
  // and the detail.value is the current value of the path
  // for example detail => {path: 'c.d' , value: 15}
  // json would look like so after set
  // {a:12, ..., c:{d:15}, ...}
  json = _.set(json, detail.path, detail.value);
});
```
