# a.js Documentation

## Introduction

**a.js** is a lightweight and versatile JavaScript Flexwork/library designed for building reactive and dynamic web components. It extends the power of native web components by introducing custom tags (`<a-script>`, `<a-closure>`, and `<a-tagDef>`) that simplify complex operations such as state management, reactivity, and scoped behavior. These custom tags integrate seamlessly with modern JavaScript, allowing developers to create dynamic user interfaces with ease.

This documentation is targeted at developers familiar with JavaScript. Knowledge about web components and proxies is useful to understand what's going one behind the scene. If you are unfamiliar with these concepts, refer to the following resources for a brief primer:

- **Web Components**: [MDN Web Components Guide](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- **JavaScript Proxies**: [MDN Proxy Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

### Key Highlights
- Enhanced web components for rapid UI development.
- Reactive variables managed through JavaScript proxies.
- Modular and reusable code through `<a-tagDef>`.
- Allow the redefine web components already declared with **a.js**
- Seamless integration with HTML and JavaScript ecosystems.

---

## Overview of Features

### Custom HTML Tags

a.js introduces three primary custom tags to enhance web development:

1. **`<a-script>`**
   - A custom replacement for the `<script>` tag with additional functionality, including scoped execution and integration with a.js features.

2. **`<a-closure>`**
   - Encapsulates JavaScript code in a closure for isolated execution, preventing global namespace pollution.

3. **`<a-tagDef>`**
   - Defines reusable custom components that can be dynamically instantiated in the DOM and re-defined when necessary.

### Special Variables and Behaviors
- **`currentScript`** and **`currentElement`**:
  - Provide references to the active `<script>` or target element within a.js tags, streamlining access to DOM elements.

- **Enhanced Reactivity**:
  - Watched variables defined within `<let>` blocks use JavaScript proxies for fine-grained reactivity, enabling automatic updates to UI elements.
  - Non watched variables defined within `<let>` blocks allows you to maintain state inside a component or between several of them without triggering reactivity.

### Modular Imports
- **`<import>`** tag:
  - Dynamically import JavaScript, CSS, or HTML fragments.
  - Includes recursive resolution of imports within `<a-xxx>` tags.

### Advanced Features
- **Scoped Variables**:
  - Manage variables at global, namespace, or local levels with fine control over their visibility and lifecycle.

- **Reactive Loops**:
  - Use `<forEachOf>` tags to create dynamically reactive lists.

- **Dynamic Keyed Elements**:
  - Optimize updates for elements inside reactive loops by using unique keys.

- **Custom Event Handling**:
  - Automatically attach reactive event listeners to DOM elements created with a.js.

---

## Custom Tags and Components

### `<a-closure>`
The `<a-closure>` tag serves as the backbone of a.js functionality. It encapsulates JavaScript code inside a closure, preventing unintended interactions with the global scope.

#### Features:
- Automatically wraps the script content in an IIFE (Immediately Invoked Function Expression).
- Ensures variable isolation and reduces the risk of conflicts.
- Offers seamless access to a.js reactive variables and utilities.

#### Syntax:
```html
<a-closure>
  // JavaScript code here
</a-closure>
```

#### Example:
```html
<a-closure>
  let localVar = 'Scoped Variable';
  console.log(localVar);
  currentElement.innerHTML = 'This is scoped.';
</a-closure>
```

### `<a-tagDef>`
The `<a-tagDef>` tag enables developers to define custom, reusable components. These components are instantiated dynamically in the DOM, promoting modular and maintainable code.

#### Features:
- Allows the creation of custom elements with specific behavior and styles.
- Simplifies code reuse and encapsulation.
- Supports redefinition for existing tags with the `redefine` attribute.

#### Syntax:
```html
<a-tagDef forTag="custom-tag">
  // Code defining behavior for <custom-tag>
</a-tagDef>
```

#### Example:
```html
<a-tagDef forTag="custom-button">
  currentElement.innerHTML = '<button>Click Me</button>';
  currentElement.addEventListener('click', () => {
    alert('Button clicked!');
  });
</a-tagDef>

<custom-button></custom-button>
```
#### `<a-tagdef>`attributes

##### `forTag`

###### Description:
The `forTag` attribute is used in `<a-tagDef>` elements to define a custom tag. This attribute specifies the name of the custom tag being defined.

###### Behavior:
- If a custom tag with the specified name already exists, a.js will throw an error unless the `redefine` attribute is present.
- It associates the `<a-tagDef>` content with the defined custom tag name.

###### Syntax:
```html
<a-tagDef forTag="custom-box">
  currentElement.innerHTML = '<div>Custom Box</div>';
</a-tagDef>

<custom-box></custom-box>
```

---

##### `redefine`

###### Description:
The `redefine` attribute allows redefining an existing custom tag. Without this attribute, a.js will ignore the redefinition attempt.

###### Behavior:
- Mandatory for redefining a tag.
- Applies only to newly connected custom tags unless `A.replayCustomTag(element)` or `A.replayCustomTagsByName("tagname")` is called.

###### Syntax:
```html
<a-tagDef forTag="custom-box" redefine>
  currentElement.innerHTML = '<div>Updated Custom Box</div>';
</a-tagDef>
```

when you add the above to the DOM, the `<custom-box>` tags that already are in the document will still display `Custom box`, but any new `<custom-box>` tag you append to the DOM will display `Updated Custom Box`.
If you want the existing `<custom-box>` tags to adopt the new definition, you have to call `A.replayCustomTag(element)` on each of them individually, or `A.replayCustomTagsByName("custom-box")` to update all of them at once.

###### Example of replaying:
```javascript
A.replayCustomTagsByName('custom-box');
```


### `<a-script>`
The `<a-script>` tag is a customized replacement for the native `<script>` tag, providing additional functionality specific to a.js. It allows developers to run JavaScript code within a controlled context while integrating deeply with a.js features.

#### Features:
- Supports scoped execution, reducing the risk of global variable pollution.
- Offers access to special variables `currentScript` and `currentElement` for targeting the relevant script and element.
- Compatible with the `closure` attribute to encapsulate the script within a closure.

#### Syntax:
```html
<a-script>
  // JavaScript code here
</a-script>
```

#### Example:
```html
<a-script>
  console.log('Executed within <a-script>');
  currentElement.innerHTML = 'Hello from a.js';
</a-script>
```

---

## Special Variables and Behaviors

### `currentScript`
`currentScript` refers to the `<script>` tag of the custom a.js tag containing the variable. This variable allows direct access to the running script element, enabling dynamic manipulation or introspection.

#### Example:
```html
<a-script>
  console.log('The script:', currentScript);
</a-script>
```

### `currentElement`
`currentElement` provides a reference to the DOM element targeted by the custom or `<a-xxxx>` a.js tag. This variable is essential for modifying the target element dynamically.

#### Example:
```html
<a-closure>
  currentElement.innerHTML = 'Hello from currentElement!';
</a-closure>
```

### (...), {(...)} and ${...}
Inside an ```<a-xxxx></a-xxxx>``` element, you can write any html tag between parenthesis `(` and `)` and before the runtime it will be converted to a javascript code generating the corresponding HTMLElement in the context of the ```<a-xxxx></a-xxxx>``` element.
- `(...)` generates the element and returns it.
#### Example:
```html
<a-closure>
  currentElement.appendChild(<b>Bold text</b>)
</a-closure>
```
will append a `<b>` containing a text node "Bold text" to currentElement

```html
<a-closure>
  document.body.appendChild(<b>Bold text</b>)
</a-closure>
```
will append a `<b>` containing a text node "Bold text" to the body

- `{(...)}` generates the element and append it to currentElement.
#### Example:
```html
<a-closure>
  {(<b>Bold text</b>)}
</a-closure>
```
will append a `<b>` containing a text node "Bold text" to currentElement

- `${...}` leverages JavaScript template litterals to allow you to display the value of any JavaScript variable or expression evaluated in the context inside `(...)` or `{(...)}`. Additionnaly, if `${...}` refers to a watched variable, the corersponding content will become reactive.
```html
<a-closure>
  <let>
  global watched bold.text;
  </let>
  bold.text = "Bold text"
  {(<b>${bold.text}</b>)}
  bold.text = "reactive bold text"
</a-closure>
```
Every time bold.text is updated to a new value, the text of the text node inside the `<b>` tag will be updated to this new value too.

---

## Utility Functions

### watch(callback[,onOkCallback][,onErrorCallback])

- Tracks changes to reactive variables and executes `callback` when changes occur. Nested watch calls optimize dependency tracking. `onOkCallback`, if provided, gets called with the result of `callback` each time it is called. `onErrorCallback` is called when a call to `callback` throws an error.
- returns `{result}` where result is what `callback` returns. The result property gets updated whenever `callback` is called.
- You can set the `suspend` property of the returned object to true to pause the calls to callback on changes.

#### Example:
```html
<a-closure>
  <let>
    global watched app.data.value;
  </let>
  app.data.value = 42;

  watch(() => {
    console.log('Value updated:', app.data.value);
  });
</a-closure>
```

### unwatch(base)

Creates a static (non-reactive) copy of the base object of a watched variable.

#### Example:
```html
<a-closure>
  <let>
    global watched app.data.value;  //app is the base object
  </let>
  app.data.value = 42;

  const staticApp = unwatch(app);
  watch(() => {
    console.log('Value updated:', staticApp.data.value); // Static value that does not trigger reactivity
  });
</a-closure>
```

### expose(function[, name])

Attaches a function to currentElement.A with a specified name.

#### Example:
```html
<a-closure>
  expose(function sayHello() {
    console.log('Hello from exposed function!');
  }, 'sayHello');

  currentElement.A.sayHello();
</a-closure>
```

### injectFunction(callback)

Wraps a function to execute within the context of the current script and element.

#### Example:
```html
<script>
  const injected = element.A.injectFunction(() => {
    console.log('Injected function context:', currentElement);
  });

  injected();
</script>
```

## `A` Global Object Methods and Functions

### `A.initialized()`
Returns a promise that resolves once a.js has fully initialized, including defining all `<a-xxxx>` tags.

#### Syntax:
```javascript
A.initialized().then(() => {
  console.log('a.js is fully initialized.');
});
```

#### Returns:
- **Promise**: Resolves when a.js is ready for use.

---

### `A.allParsed()`
Returns a promise that resolves once all `<a-xxxx>` elements present at initialization have been parsed.

#### Syntax:
```javascript
A.allParsed().then(() => {
  console.log('All a-elements have been parsed.');
});
```

#### Returns:
- **Promise**: Resolves when all `<a-xxxx>` elements are fully parsed.

---

### `A.waitUntilAParsedIfExists(element)`
Waits for a specified element to be parsed if it exists. If the element does not exist, the promise resolves immediately.

#### Parameters:
- **`element`** (HTMLElement): The element to wait for.

#### Returns:
- **Promise**: Resolves when the specified element has been parsed or if it does not exist.

#### Syntax:
```javascript
A.waitUntilAParsedIfExists(document.querySelector('my-element')).then(() => {
  console.log('my-element parsed or does not exist.');
});
```

---

### `A.dumpAllStates()`
Dumps all `<let>` variable states into a JSON string. This is useful for persisting the application state.

#### Returns:
- **String**: A JSON string representing the current state of all `<let>` variables.

#### Syntax:
```javascript
const stateDump = A.dumpAllStates();
console.log(stateDump);
```

---

### `A.restoreAllStates(dump)`
Restores `<let>` variable states from a previously dumped state string.

#### Parameters:
- **`dump`** (String): The JSON string returned by `A.dumpAllStates`.

#### Syntax:
```javascript
A.restoreAllStates(stateDump);
```

#### Behavior:
- Reinstates the values of variables defined in `<let>` tags.
- Handles scope-specific variables using the HTML tree structure.

---

### `A.importIn(elem, url[, type][, removeAfterImport][, onloaded])`
Imports content from the specified `url` into the given element `elem` by creating an `<a-closure><import>` in it. This can dynamically load HTML, CSS, JavaScript, or other resources.

#### Parameters:
- **`elem`** (HTMLElement): The target element for the imported content.
- **`url`** (String): The URL to import from.
- **`type`** (String, optional): The type of content to import. Defaults to `text/html`. Other valid values include `css`, `javascript`, `json`.
- **`removeAfterImport`** (Boolean, optional): If `true`, removes the `<a-closure>` tag after execution. Defaults to `true`.
- **`onloaded`** (Function, optional): A callback function executed after the import is complete.

#### Syntax:
```javascript
A.importIn(currentElement, './fragment.html', 'html', true, () => {
  console.log('Import completed.');
});
```

---

### `A.getOrCreateKeyedElement(tagName, key, htmlElement[, namespace])`
Retrieves or creates a keyed element associated with the provided `key`.

#### Parameters:
- **`tagName`** (String): The tag name of the element to create.
- **`key`** (String): A unique identifier for the element.
- **`htmlElement`** (HTMLElement): The parent element.
- **`namespace`** (String, optional): A namespace for the key.

#### Returns:
- **HTMLElement**: The created or retrieved keyed element.

#### Syntax:
```javascript
const keyedElem = A.getOrCreateKeyedElement('div', 'unique-key', document.body);
```

---

### `A.removeKeyedElement(key, htmlElement[, namespace])`
Removes a keyed element from the DOM and deletes its key association.

#### Parameters:
- **`key`** (String): The unique identifier of the element.
- **`htmlElement`** (HTMLElement): The parent element.
- **`namespace`** (String, optional): A namespace for the key.

#### Returns:
- **HTMLElement**: The removed element, if it exists.

#### Syntax:
```javascript
A.removeKeyedElement('unique-key', document.body);
```

---

### `A.getAllKeyedElementsKeysArray(htmlElement[, namespace])`
Retrieves an array of all keys associated with a specific element and namespace.

#### Parameters:
- **`htmlElement`** (HTMLElement): The parent element.
- **`namespace`** (String, optional): A namespace for the keys.

#### Returns:
- **Array**: An array of keys.

#### Syntax:
```javascript
const keys = A.getAllKeyedElementsKeysArray(document.body);
console.log(keys);
```

---

### `A.getAllKeyedElementsKeysSet(htmlElement[, namespace])`
Retrieves an array of all keys associated with a specific element and namespace.

#### Parameters:
- **`htmlElement`** (HTMLElement): The parent element.
- **`namespace`** (String, optional): A namespace for the keys.

#### Returns:
- **Set**: A Set of keys.

#### Syntax:
```javascript
const keys = A.getAllKeyedElementsKeysArray(document.body);
console.log(keys);
```

---

### `A.getAllKeyedElementsKeysObject(htmlElement[, namespace])`
Retrieves an array of all keys associated with a specific element and namespace.

#### Parameters:
- **`htmlElement`** (HTMLElement): The parent element.
- **`namespace`** (String, optional): A namespace for the keys.

#### Returns:
- **Object**: An object { key : true} of keys.

#### Syntax:
```javascript
const keys = A.getAllKeyedElementsKeysArray(document.body);
console.log(keys);
```


---

### `A.updateObject(target, source)`
Merges the properties of the `source` object into the `target` object. Only differing properties are updated.

#### Parameters:
- **`target`** (Object): The object to be updated.
- **`source`** (Object): The object providing new values.

#### Returns:
- **void**

#### Syntax:
```javascript
let target = { a: 1, b: 2 };
let source = { b: 3, c: 4 };
A.updateObject(target, source);
console.log(target); // { a: 1, b: 3, c: 4 }
```

---

### `A.getJSONPath(obj, tpath[, value])`
Retrieves or sets a value in an object or array using a JSON path.

#### Parameters:
- **`obj`** (Object | Array): The target object or array.
- **`tpath`** (String): The JSON path.
- **`value`** (Any, optional): The value to set at the specified path.

#### Returns:
- **Any**: The value at the specified path, or the updated object.

#### Syntax:
```javascript
let data = { a: { b: [1, 2, 3] } };
const value = A.getJSONPath(data, 'a.b.1');
console.log(value); // value is 2 => logs 2

A.getJSONPath(data, 'a.b.1', 42);
console.log(data); // data has been modified => logs { a: { b: [1, 42, 3] } }
```

---

### `A.getNonATextContent(element)`
Retrieves the content of an element, excluding everything that is an a-element or inside one.

#### Parameters:
- **`element`** (HTMLElement): The element whose text content is to be retrieved.

#### Returns:
- **String**: The plain text content of the element.

#### Syntax:
```javascript
const text = A.getNonATextContent(document.querySelector('my-element'));
console.log(text);
```

---

### `A.getTextContent(element)`
Retrieves the content of an element, including content inside `<a-xxxx>` elements but removes any `<script type="a/unparsed">` and the corresponding closing tag.

#### Parameters:
- **`element`** (HTMLElement): The element whose text content is to be retrieved.

#### Returns:
- **String**: The text content of the element, including content from a.js elements.

#### Syntax:
```javascript
const text = A.getTextContent(document.querySelector('my-element'));
console.log(text);
```

---

### `A.getSourceContent(element)`
Retrieves the content of an element, as it would appear when explored in the browser developer tools Elements tab.

#### Parameters:
- **`element`** (HTMLElement): The element whose source content is to be retrieved.

#### Returns:
- **String**: The raw HTML content of the element.

#### Syntax:
```javascript
const source = A.getSourceContent(document.querySelector('my-element'));
console.log(source);
```

---

## Advanced Functionalities

### Scoped Variables
Scoped variables are declared inside an ```<a-xxxx></a-xxxx>``` element in a ```<let></let>``` block, and initialized after the block. 
The variable name must contain a dot ```.``` or be an object if the name doesn't contain a dot.
Scoped variables in a.js allow developers to manage reactivity at different levels:

#### Types of Scoped Variables

1. **Global Scoped Variables**
   - Accessible across all `<a-xxxx>` elements.
   - Declared with `global`.
   - Example:
     ```html
     <let>
       global g.variable;
     </let>
     g.variable = "value"
     ```

2. **Namespace Scoped Variables**
   - Shared only among elements with the same `namespace` attribute value.
   - Declared with `nsGlobal`.
   - Example:
     ```html
     <let>
       nsGlobal ns.variable;
     </let>
     ns.variable = ["value1",2,3]
     ```

3. **Descendant Scoped Variables**
   - Shared among elements that are descendants of the element.
   - Declared with `dGlobal`. Usually in a `<a-tagDef forTag="my-ancestor">` element
   - Example:
     ```html
     <let>
       dGlobal d.variable;
     </let>
     d.variable = { name : "value" }
     ```

4. **Ancestor Scoped Variables**
   - Looks for variables declared in ancestor elements and shares them with child elements.
   - Declared with `aGlobal`. Usually in a `<a-tagDef forTag="my-descendant">` element
   - Example:
     ```html
     <let>
       aGlobal d.variable;
     </let>
     //here, in the scope, d.variable has the value { name : "value" } as long as the element in inside a `<my-ancestor><my-ancestor>`
     ```

5. **Local Scoped Variables**
   - Restricted to a single `<a-xxxx>` element.
   - Declared with `local`.
   - Example:
     ```html
     <let>
       local l.variable;
     </let>
     l.variable = 3
     ```

#### Example Usage
```html
<a-closure>
  <let>
    global g.value;
    nsGlobal ns.value;
    dGlobal d.value;
    aGlobal a.value;
    local l.value;
  </let>

  watch(() => {
    console.log('Global:', g.value);
    console.log('Namespace:', ns.value);
    console.log('Descendant:', d.value);
    console.log('Ancestor:', a.value);
    console.log('Local:', l.value);
  });
</a-closure>
```
#### Initialization of variables
Initialize the variables after the closing `</let>`of the declaration.

#### Watched variables
Any variable having the keyword `watched` between the type (`global`,...) and it's name.
#### Example
```html
<a-closure>
  <let>
    global watched global.value;
  </let>
  global.value = 42;
</a-closure>
```


### Reactive Loops with `<foreachof>`
The `<foreachof>` tag creates reactive loops that dynamically update the DOM when the underlying data changes.

#### Syntax:
```html
<foreachof a.jsvariable="value, index">
  {(<div>${...}</div>)}
</foreachof>
```

#### Example:
```html
<a-closure>
  <let>
    global watched g.items = ["Item 1", "Item 2", "Item 3"];
  </let>
  <foreachof a.jsvariable="g.items, idx">
    {(<div key="${idx}">${g.items[idx]}</div>)}
  </foreachof>
</a-closure>
```

### Custom Tags and <a-xxxx> Elements attributes

#### `singleton`

##### Description:
The `singleton` attribute ensures only one instance of an element with the same singleton name exists. Any new element with the same name will be automatically removed from the DOM before a.js does any parsing/execution.

##### Behavior:
- Guarantees a single instance per name.
- Useful for ensuring unique global components.

##### Syntax:
```html
<a-closure singleton="unique-component">
  <div>Unique Content</div>
</a-closure>
```

---

#### `singletonreplace`

##### Description:
The `singletonreplace` attribute allows replacing existing singletons.
- if the attribute value is "existing", then the new singleton will replace the previous one **and be moved at the same spot in the DOM than the previous singleton** no matter where it appeared in the DOM.
- if the attribute value is "false", the element is automtically removed
- if the attribute value is anything else, the existing singleton is removed, and the new one is the new singleton. The new singleton isn't moved, and stays where it is in the DOM.

##### Behavior:
- Forces the replacement of an existing singleton.

##### Syntax:
```html
<a-closure singleton="unique-component" singletonreplace>
  <div>Replaced Content</div>
</a-closure>
```

---

#### `waitFor`

##### Description:
The `waitFor` attribute delays the parsing and execution of an element until specified conditions are met. It uses a chain of selectors to resolve dependencies.

##### Behavior:
- The array of selectors is resolved sequentially, with each dependent on the previous.
- Ensures the required elements are parsed before proceeding.

##### Syntax:
```html
<a-closure waitFor='["my-layout", "#main", "my-page"]'>
  <div>Dependent Content</div>
</a-closure>
```

##### Example:
- Resolves `my-layout` in the document.
- Waits for `#main` within `my-layout`.
- Finally, waits for `my-page` within `#main`.


# Modular Imports in a.js

## Overview
The `<import>` tag in a.js allows developers to dynamically include external JavaScript, CSS, or HTML resources into their projects, but it must be used within `<a-xxxxx>` elements. This ensures that imports are scoped to the containing custom element, preventing unintended side effects on unrelated parts of the application.

### Key Features
1. **Recursive Import Resolution**: Handles dependencies between imported modules.
2. **Scoped Resource Application**: Automatically scoped by the enclosing `<a-xxxxx>` element.
3. **Dynamic Resource Loading**: Allows runtime inclusion of resources using the `A.importIn` function.
4. **Pre-parsing Behavior**: Imports are processed before any other content in the enclosing `<a-xxxxx>` element, influencing the final structure and behavior of the element.

---

## Syntax
The `<import>` tag uses the following attributes:

- **`src`**: Specifies the URL or path of the resource to import. Supports JavaScript (within `<a-xxxxx>` elements), CSS, and HTML files.
- **`type`**: Defines the type of the resource (e.g., `"javascript"`, `"css"`, or `"html"`). Defaults to a script/text import (inside an `<a-xxxxx>` element) if omitted.

### Example
```html
<a-closure>
  <import src="./styles/main.css" type="css"></import> //import the styles in main.css in a way that only applies them to the children of currentElement
  <import src="./scripts/utils.js" type="javascript"></import> //replace this <import></import> tag by the text of the ./scripts/utils.js file. If this text contains an import tag, it will be resolved too.
  <import src="./components/header.html" type="html"></import> //import the content of ./components/header.html as child nodes of currentElement.
</a-closure>
```

---

## Detailed Behavior

### Pre-parsing Behavior
Imports are the first elements to be processed inside an `<a-xxxxx>` element. This means that they are parsed and executed before any other content or logic within the element. 
> [!CAUTION]
> This means it (usually) makes no sense to write something like
```html
<a-closure>
  currentElement.innerHTML = '<import src="./styles/dark-theme.css" type="css"></import>';
</a-closure>
```

Because at runtime, the `<import>` will have been processed, and the script inside `<a-closure>` will see the following:
```html
<a-closure>
  currentElement.innerHTML = '';
</a-closure>
```

### Recursive Import Resolution
When an imported resource references additional dependencies, a.js resolves these recursively. This ensures that all necessary resources are loaded before the application interacts with them.

#### Example
If `utils.js` contains `<import src="./scripts/helpers.js"></import>`, the following code ensures both are loaded:

```html
<a-closure>
  <import src="./scripts/utils.js" type="javascript"></import>
</a-closure>
```

### Dynamic Resource Loading
Dynamic resource loading can be accomplished using `A.importIn`, which programmatically loads resources into a specified element.

#### Example
```javascript
A.importIn(currentElement, './html/layout.html', "html");
```

---

## Practical Use Cases

### Modular Component Development
By importing HTML fragments as custom components, developers can keep their code modular and reusable.

#### Example
```html
<a-tagDef forTag="custom-card">
  <import src="./components/card.tagdef"></import>
</a-tagDef>

<custom-card></custom-card>
```

### Dynamic Style Application
Switching themes or applying styles dynamically based on user actions is simplified with `A.importIn`.

#### Example
```html

<a-closure>
  {(<button onclick="loadTheme('dark')">Dark Theme</button>)}
  {(<button onclick="loadTheme('light')">Light Theme</button>)}
  let loaded = null;
  function loadTheme(theme) {
  	if (loaded) {
		loaded.obj.remove();
	}
    A.importIn(currentElement, `./styles/${theme}-theme.css`, 'css',(elem,url,type,imports) => {
		loaded = imports.css[0]
	},true });
  }
</a-closure>
```

---

## Best Practices
1. **Minimize Overuse**: Avoid overloading the application with too many `<import>` tags to maintain performance.
2. **Understand Pre-parsing**: Remember that imports are processed before any other logic in the enclosing `<a-xxxxx>` element.
3. **Lazy Loading**: Utilize `A.importIn` to load resources dynamically and optimize page load times.

---

By using the `<import>` tag effectively, developers can create highly modular, dynamic, and maintainable web applications with a.js.

# Reactive State Management in a.js

## Overview
Reactive state management is a core feature of a.js, enabling developers to create dynamic user interfaces with minimal effort. By leveraging JavaScript proxies and the declarative nature of custom tags, a.js simplifies the process of managing and reacting to changes in application state.

### Key Features
1. **Watched Variables**: Track changes to specific variables and update the UI or trigger logic automatically.
2. **Reactive Expressions**: Define computed properties or logic that depend on watched variables.
3. **Efficient Updates**: Minimize unnecessary DOM updates by intelligently tracking dependencies.
4. **Declarative Syntax**: Use `<let>` and `watch` constructs to simplify reactive state declaration.

---

## Core Concepts

### Watched Variables
Watched variables are declared using the `<let>` tag, enabling automatic tracking of changes. These variables are wrapped in JavaScript proxies, allowing for seamless reactivity. Note that variable names in `<let>` must contain a dot (e.g., `counters.count`). Initialization should occur outside the `<let>` declaration.

#### Example:
```html
<a-closure>
  <let>
    global watched counters.count;
  </let>

  counters.count = 0;

  currentElement.addEventListener('click', () => {
    counters.count++;
  });
  {(<div>${counters.count}</div>)}
</a-closure>
```

In this example, the `counters.count` variable is watched, and changes to its value can trigger reactive updates in the UI or dependent logic.

### Reactive Expressions
Reactive expressions are computations or logic that automatically re-evaluate when their dependencies change. They can be defined using the `watch` function.

#### Example:
```html
<a-closure>
  <let>
    global watched values.x;
    global watched values.y;
  </let>

  values.x = 10;
  values.y = 20;

  const watcher = watch(() => {
    console.log('Sum:', values.x + values.y);
  });

  watcher.suspend = true; // Temporarily suspend watching
  watcher.suspend = false; // Resume watching
```

When either `values.x` or `values.y` changes, the expression inside `watch` is re-evaluated, and the new sum is logged to the console. By toggling the `suspend` property, developers can control when the watcher reacts.

### Dependency Tracking
a.js employs fine-grained dependency tracking to optimize reactivity. Only the parts of the DOM or logic that depend on updated variables are re-evaluated, ensuring efficient updates.

---

## Advanced Reactive Constructs

### Unwatching Variables
The `unwatch` function creates a deproxified version of a root watched variable. This unwatched version does not trigger observations inside a `watch` function, allowing for static access to a variable's value without reactive behavior.

#### Example:
```html
<a-closure>
  <let>
    global watched data.value;
  </let>

  data.value = 42;

  const staticData = unwatch(data);

  watch(() => {
    console.log(staticData.value); // This will not trigger when data.value updates
  });
```

In this example, `staticData` remains a static copy of `data` and does not participate in the reactivity system, but any update to `data.value` will be reflected in `staticData.value` and any update to `staticData.value` be reflected to `data.value` (and thus would trigger watched `data.value`)

### Reactive Scopes
Reactive variables can be scoped to specific levels (e.g., `global`, `local`, `nsGlobal`). This ensures that changes to variables only affect the intended parts of the application.

#### Example:
```html
<a-closure>
  <let>
    global watched app.globalValue;
    local watched app.localValue;
  </let>

  app.globalValue = 42;
  app.localValue = 'hello';

  watch(() => {
    console.log('Global:', app.globalValue);
    console.log('Local:', app.localValue);
  });
</a-closure>
```

### Batched Updates
a.js intelligently batches updates to minimize redundant computations or DOM changes. Multiple changes within the same execution context are applied together.

#### Example:
```html
<a-closure>
  <let>
    global watched counters.a;
    global watched counters.b;
  </let>

  counters.a = 1;
  counters.b = 2;

  watch(() => {
    console.log('Sum:', counters.a + counters.b);
  });

  // Batch changes
  counters.a = 10;
  counters.b = 20;
</a-closure>
```

In this example, the reactive expression recalculates only once after both `counters.a` and `counters.b` are updated.

---

## Best Practices
1. **Use Scoped Variables**: Scope variables appropriately to prevent unintended dependencies.
2. **Avoid Overwatching**: Only track variables that require reactivity.
3. **Leverage `unwatch` for Static Access**: Use `unwatch` to create static versions of reactive variables when needed.
4. **Clean Up Watches**: Use the `suspend` property or `unwatch` to manage unused reactive logic.
5. **Initialize Outside `<let>`**: Declare variables in `<let>` without initialization, and assign their values afterward.

By understanding and applying these constructs, developers can create highly efficient and dynamic interfaces using a.js's reactive state management.

# Custom Events and Event Handling in a.js

## Overview

Event handling in a.js is designed to simplify interaction management by providing a reactive, declarative approach to custom events and event listeners. This system ensures seamless integration between dynamic DOM updates and user-triggered events, supporting modularity and scalability in application development.

### Key Features

1. **Declarative Binding**: Attach event listeners directly in your custom components without verbose JavaScript.
2. **Reactive Listeners**: Dynamically update event handlers when reactive variables change.
3. **Custom Events**: Create and dispatch events specific to your application needs.
4. **Scoped Handlers**: Automatically scope event handling to specific custom components, ensuring modular behavior.
5. **Event Delegation**: Optimize event handling for dynamic lists and nested elements.

---

## Conceptual Benefits

1. **Declarative Syntax**:

   - a.js embraces declarative programming, allowing developers to define event behavior inline with component markup.
   - Example: Binding a click event directly in an `<a-closure>` tag simplifies managing interactive behaviors.

2. **Reactivity**:

   - Event handlers can respond to changes in reactive variables without requiring explicit reattachment.
   - Example: A button's click handler could vary based on application state, dynamically adjusting its behavior.

3. **Custom Events**:

   - Developers can define and dispatch custom events to facilitate communication between components.
   - Example: Dispatching a `user-logged-in` event from a login form and listening for it in a parent container.

4. **Scoped and Modular**:

   - Event listeners are automatically scoped to the specific component they are defined in, reducing the risk of unintended side effects.

5. **Event Delegation**:

   - a.js provides tools to manage event listeners efficiently for dynamically created elements, reducing memory overhead and improving performance.

---

## Detailed Syntax and Examples

### Declarative Binding
Event listeners in a.js can be attached directly within component markup using the native `onevent` syntax, similar to standard JavaScript.

#### Example:
```html
<a-closure>
  <let>
    global app.handleClick;
  </let>

  app.handleClick = function(event) {
    console.log('Button clicked:', event.target);
  };

  {(<button onclick="app.handleClick(event)">Click Me</button>)}
</a-closure>
```

In this example, clicking the button triggers the `handleClick` function.

### Dynamic Event Listeners
Dynamic event listeners enable the behavior of events to change based on application state. 

#### Example:
```html
<a-closure>
  <let>
    global app.buttonAction;
  </let>

  app.buttonAction = function(event) {
    console.log('Default action');
  };

  {(<button onclick="app.buttonAction(event)">Dynamic Button</button>)}
  
  app.buttonAction = function(event) {
    console.log('Updated action');
  };

</a-closure>
```

The `app.buttonAction` function dynamically updates its behavior without requiring reattachment.

### Custom Events
Custom events allow components to emit application-specific events, enhancing inter-component communication.

#### Example:
```html
<a-closure>
  {(<button onclick="currentElement.dispatchEvent(new CustomEvent('customEvent', { detail: { data: 'Example' } }))">
    Emit Event
  </button>)}

  currentElement.addEventListener('customEvent', function(event) {
    console.log('Received custom event:', event.detail.data);
  });
</a-closure>
```

Here, clicking the button emits a `customEvent` that other parts of the application can listen for.

### Event Delegation
Event delegation optimizes performance by attaching a single listener to a parent element to handle events for its children dynamically.

#### Example:
```html
<a-closure>
  <let>
    global app.handleListClick;
  </let>

  app.handleListClick = function(event) {
    if (event.target.tagName === 'LI') {
      console.log('Clicked item ID:', event.target.dataset.id);
    }
  };

  {(<ul onclick="app.handleListClick(event)">
    <li data-id="1">Item 1</li>
    <li data-id="2">Item 2</li>
    <li data-id="3">Item 3</li>
  </ul>)}
</a-closure>
```

This approach reduces memory usage and improves performance, particularly for dynamically generated lists.

---

## Best Practices

1. **Keep Event Handlers Modular**: Define handlers as separate functions for readability and reusability.
2. **Utilize Dynamic Listeners When Needed**: Adjust event behavior dynamically without overengineering reactivity.
3. **Avoid Overbinding**: Minimize the number of individual event listeners by using delegation where applicable.
4. **Emit Specific Custom Events**: Use custom events to keep component communication clear and encapsulated.

By understanding and using these patterns, developers can build highly interactive and maintainable applications with a.js.


# Debugging and Testing a.js Applications

## Overview
Debugging and testing are essential to ensure the robustness and maintainability of applications built with a.js. By leveraging modern browser tools, a.js's reactive debugging capabilities, and effective testing strategies, developers can quickly identify and resolve issues, optimize performance, and ensure consistent behavior.

### Key Areas of Focus
1. **Browser Developer Tools**: Utilize browser-based tools for inspecting DOM changes, event listeners, and JavaScript execution.
2. **Debugging Reactive Variables**: Monitor the state and updates of reactive variables and understand their effects on the application.
3. **Component Testing**: Write focused unit and integration tests for custom components to ensure predictable behavior.
4. **Event Handling Validation**: Verify that event listeners and custom events function as intended in various scenarios.
5. **Performance Profiling**: Identify bottlenecks in reactivity and DOM updates to optimize application responsiveness.

---

## Conceptual Benefits

1. **Enhanced Debugging Tools**:
   - a.js is designed to integrate smoothly with browser developer tools, providing clarity in inspecting custom elements, reactive variables, and their dependencies.
   
2. **Modular Testing**:
   - Testing individual components and custom tags ensures that each module behaves predictably and independently.

3. **Proactive Optimization**:
   - Debugging tools and testing methodologies enable developers to identify and address performance issues before they impact users.

4. **Improved Collaboration**:
   - Clear debugging logs, readable tests, and consistent methodologies enhance team collaboration and code maintainability.

---

## Leveraging Browser Developer Tools

### Inspecting Reactive Variables
Use the browser console to directly interact with and inspect reactive variables defined within your application.

#### Example:
```html
<a-closure>
  <let>
    global watched app.data.value;
  </let>

  app.data.value = 42;
  console.log(app.data.value); // Logs: 42
</a-closure>
```
In the browser console, you can modify `app.data.value` and observe changes in real time, reflecting the reactivity in your application.

### Debugging Event Listeners
Inspect event listeners attached to elements using browser developer tools.

#### Steps:
1. Open the **Elements** tab in your browser's developer tools.
2. Select the element to which an event listener is attached.
3. View the event listeners under the **Event Listeners** section.

This helps you confirm that event bindings are correctly applied and scoped.

---

## Debugging Reactive Variables

### Monitoring Variable Updates
Reactive variables in a.js are wrapped in proxies, which can be inspected to trace their changes. Use the `watch` function to log updates.

#### Example:
```html
<a-closure>
  <let>
    global watched app.state.counter;
  </let>

  app.state.counter = 0;

  watch(() => {
    console.log('Counter updated:', app.state.counter);
  });

  app.state.counter++; // Triggers the watch function
</a-closure>
```

---

## Writing Unit and Integration Tests

### Unit Testing Custom Components
Focus on testing isolated functionality of custom tags defined using `<a-tagDef>`.

#### Example:
```html
<a-tagDef forTag="custom-button">
  {(<button onclick="console.log('Button clicked')">Click Me</button>)}
</a-tagDef>

<custom-button></custom-button>
```
Use tools like Jasmine or Jest to simulate user interactions and verify outputs.

### Integration Testing
Combine multiple components and verify their interaction in a cohesive flow.

#### Example:
```html
<a-closure>
  <let>
    global watched app.state.message;
  </let>

  app.state.message = 'Hello';
  {(<p>${app.state.message}</p>)}

  setTimeout(() => {
    app.state.message = 'Updated Message';
  }, 1000);
</a-closure>
```
Test that the `p` element updates its content dynamically after the state change.

---

## Performance Profiling

### Measuring Reactive Updates
Use browser performance tools to profile reactivity and DOM updates.

#### Steps:
1. Open the **Performance** tab in developer tools.
2. Record a session while interacting with your application.
3. Analyze the timeline for excessive computations or unnecessary DOM updates.

---

## Common Debugging Pitfalls

### Overwatching Variables
Avoid watching variables unnecessarily, which can lead to performance overhead.

### Improper Scope
Ensure variables are scoped correctly to avoid unintended side effects or untracked dependencies.

By adhering to these debugging and testing methodologies, you can build reliable, efficient, and maintainable a.js applications.

# Extending and Adapting a.js

The extension system in a.js allows developers to define custom behaviors, inject new functionalities, and override existing ones. It offers flexibility for advanced customization while maintaining the reactive and modular nature of a.js.

---

## **Extensions with `<script a-extension>`**

### Description:
The `<script a-extension>` tag is used to define extensions in a.js. These extensions allow developers to add, replace, or inject functions into the `A` global object or any custom tags defined in a.js.

### Syntax:
```html
<script a-extension>
  // Define or override functions here
</script>
```

### Behavior:
1. Extensions are processed during the initialization phase of a.js.
2. The script tag is associated with an `_AExtension` object containing extension-specific properties and tasks.

#### Example:
```html
<script a-extension>
  function f(elem) {
    let src = A.getSourceContent(elem).txt;
    console.log(src);
  }
  document.currentScript._AExtension = {
    AParsedTagsTasks: [
      { selector: "console", function: f }
    ]
  };
</script>
```

---

## **`A.extend` Object**

The `A.extend` object provides methods to add or override internal functions in a.js during an extension's execution.

### **`addInitWait(promise)`**
Adds a promise to the initialization process, ensuring specific tasks are completed before a.js is considered fully initialized.

#### Parameters:
- **`promise`** (Promise): A promise that must resolve before initialization completes.

#### Example:
```javascript
A.extend.addInitWait(new Promise((resolve) => setTimeout(resolve, 1000)));
```

---

### **`getInternalFunction(AFunctionName)`**
Retrieves the internal implementation of an existing function.

#### Parameters:
- **`AFunctionName`** (String): The name of the function to retrieve.

#### Returns:
- **Function**: The internal implementation of the function.

#### Example:
```javascript
const getJSONPath = A.extend.getInternalFunction("getJSONPath");
console.log(getJSONPath);
```

---

### **`replaceInternalFunction(AFunctionName, newFunction)`**
Replaces an existing internal function with a new implementation.

#### Parameters:
- **`AFunctionName`** (String): The name of the function to replace.
- **`newFunction`** (Function): The new implementation of the function.

#### Example:
```javascript
A.extend.replaceInternalFunction("getJSONPath", function (obj, path) {
  console.log("Custom behavior");
  return path.split(".").reduce((o, p) => o && o[p], obj);
});
```

---

### **`addInternalFunction(AFunctionName, newFunction)`**
Adds a new internal function to a.js.

#### Parameters:
- **`AFunctionName`** (String): The name of the new function to add.
- **`newFunction`** (Function): The implementation of the new function.

#### Returns:
- **Function**: The added function.

#### Example:
```javascript
const newFunc = A.extend.addInternalFunction("myNewFunction", () => {
  console.log("This is a new function.");
});
newFunc();
```

---

## **Using `_AExtension`**

The `_AExtension` object in an extension script can define tasks that are executed when custom tags are parsed.

### **`AParsedTagsTasks`**

#### Description:
The `AParsedTagsTasks` property defines tasks to execute for specific tags when they are parsed.

#### Properties:
- **`selector`** (String): A CSS selector to match specific tags.
- **`function`** (Function): A function to execute for each matched tag.

#### Example:
```javascript
function logSourceContent(elem) {
  const src = A.getSourceContent(elem).txt;
  console.log("Source content:", src);
}

document.currentScript._AExtension = {
  AParsedTagsTasks: [
    { selector: "console", function: logSourceContent }
  ]
};
```

---

### **`PostImportFetchTasks`**

#### Description:
The `PostImportFetchTasks` property in `_AExtension` allows developers to process imported content after it has been fetched and before it is parsed or rendered.

#### Properties:
- **`function`** (Function): A function to process the fetched content.
  - The function receives the raw content as input.
  - The returned value should include the modified content.

#### Example:
```javascript
function cleanUnwantedTags(txt) {
  if (txt.startsWith("<!DOCTYPE ")) {
    let pos = txt.indexOf(">");
    txt = txt.substring(pos + 1);
  }
  let re = /<!--AUnwanted-->.*?<!--\/AUnwanted-->/gims;
  txt = txt.replace(re, "");
  return { txt };
}

document.currentScript._AExtension = {
  PostImportFetchTasks: [
    { function: cleanUnwantedTags }
  ]
};
```

---

### **`TagCreationTasks`**

#### Description:
The `TagCreationTasks` property defines tasks that modify or initialize elements as they are created.

#### Properties:
- **`selector`** (String): A CSS selector to identify target tags.
- **`function`** (Function): A function executed during tag creation. This function can modify the tag's behavior or properties.

#### Example:
```javascript
function initializeCustomTag(element) {
  element.setAttribute("initialized", "true");
  console.log("Tag initialized:", element.tagName);
}

document.currentScript._AExtension = {
  TagCreationTasks: [
    { selector: "custom-element", function: initializeCustomTag }
  ]
};
```

---

## **Adapting Default Configurations**

### **`AConfig` Object**

The `AConfig` object allows for overriding default behaviors and settings in a.js.

#### Modifiable Properties:
- **`addSourceMaps`** (Boolean): Adds source maps for debugging.
- **`keepCustomTagsInitState`** (Boolean): Preserves the initialization state of custom tags.
- **`noAExtend`** (Boolean): Disables the `A.extend` object if `true`.

#### Example:
```javascript
<script>
  AConfig.addSourceMaps = true;
  AConfig.keepCustomTagsInitState = false;
</script>
```

---

## **Best Practices for Extensions**

1. **Avoid Collisions**: Use unique names for new functions to prevent overwriting existing implementations unintentionally.
2. **Test Extensions Thoroughly**: Extensions can have far-reaching effects, especially when replacing internal functions.
3. **Follow Dependency Order**: Use `addInitWait` to ensure dependencies are resolved before your extension executes.
4. **Use Namespaces**: Scope variables and keys within namespaces to avoid conflicts in dynamic contexts.

