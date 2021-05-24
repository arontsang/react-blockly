# react-blockly

[![Built on Blockly](https://tinyurl.com/built-on-blockly)](https://github.com/google/blockly)

A React library for embedding [Blockly](https://developers.google.com/blockly/), the visual programming editor from Google.

This is a continuation of PatientsLikeMe's [react-blockly-component](https://github.com/patientslikeme/react-blockly-component) project.  This new version utilizes Google's official Blockly npm packages in addition to other modernizations.

Features:

* Provides a hook as well as a function component for ease of use + flexibility
* Supports the JSON toolbox format
* Automatically propagates prop updates to Blockly
* Provides callbacks for workspace injection, disposal, changes, and XML import errors
* Automatically generates workspace XML, debounced for performance

## VERSION 7 IS A COMPLETE REWRITE

This is the README for react-blockly version 7 (currently in pre-release).  If you're using the old class-based components, you will want to look at the version 6 readme instead: https://github.com/nbudin/react-blockly/blob/v6-stable/README.md

The breaking changes in react-blockly 7 are:

* All components except BlocklyWorkspace have been removed
* A hook for embedding Blockly in custom DOM has been added
* The custom JSON format for toolbox has been removed in favor of Blockly's official toolbox JSON format
* React 16.8 or higher is required
* immutable.js has been removed

## Example usage

**NOTE: The pre-release react-blockly 7 breaks this example; I'm working on updating it.  Stay tuned!**

[FernandoVazZ](https://github.com/FernandoVazZ) has made an example React app that shows how to set up react-blockly as well as how to create custom blocks for use in it: https://github.com/FernandoVazZ/reactblockly-customblocks/

Thanks so much Fernando!

## Help wanted!

I wrote react-blockly for an internal tool when I worked at PatientsLikeMe, Inc.  I no longer maintain this tool, and I don't even have access to it anymore.  I also don't have any actual project that uses react-blockly anymore.

I'd love to find maintainers for this package who actually use it.  In particular, some things I would love to have help with are:

* A test suite
* TypeScript typings

If you are interested in working on any of those things, please [let me know](mailto:natbudin@gmail.com)!  Additionally, if you're interested in taking over maintainership of this package entirely, I'd be happy to talk to you about that.

## Installation

To add react-blockly to a React app that uses npm, run:

```bash
npm install --save react-blockly
```

To add react-blockly to a React app that uses yarn, run:

```bash
yarn add react-blockly
```

## How to use

You can use react-blockly as either a component or a hook.  Embedding a component is often more
straightforward and plugs into your app easily.  On the other hand, using the hook can give you
more control over exactly how Blockly is rendered.

### Embedding a component

Write `import { BlocklyWorkspace } from 'react-blockly';` in your code and use BlocklyWorkspace as a component.

Example:

```jsx
import { BlocklyWorkspace } from 'react-blockly';

function MyBlocklyEditor() {
  const [xml, setXml] = useState();

  return (
    <BlocklyWorkspace
      className="width-100" // you can use whatever classes are appropriate for your app's CSS
      toolboxConfiguration={MY_TOOLBOX} // this must be a JSON toolbox definition
      initialXml={xml}
      onXmlChange={setXml}
    />
  )
}
```

### Using the hook

Write `import { useBlocklyWorkspace } from 'react-blockly';` in your code and use this hook to inject a Blockly workspace into your rendered components.

Example:

```jsx
import { useBlocklyWorkspace } from 'react-blockly';

function MyBlocklyHookEmbed() {
  const blocklyRef = useRef(null);
  const { workspace, xml } = useBlocklyWorkspace({
    ref: blocklyRef,
    toolboxConfiguration: MY_TOOLBOX, // this must be a JSON toolbox definition
    initialXml: xml,
  });

  return (
    <div ref={blocklyRef} /> // Blockly will be injected here
  )
}
```

## API reference

### BlocklyWorkspace component props

All properties are optional.

* `initialXml`: The XML of the program to initially load in the editor.
* `workspaceConfiguration`: Any configuration options to be passed into `Blockly.inject` (except for `toolbox`, which is a separate prop).
* `toolboxConfiguration`: A JSON toolbox configuration (see [the Blockly documentation](https://developers.google.com/blockly/guides/configure/web/toolbox#json) for details on this format).
* `className`: The value for the `class` attribute to be used on the `<div>` elements generated by this component.  Typically you'll need to use this to set the height of the Blockly editor, using either an explicit `height` style, flexboxes, or some other means.
* `onWorkspaceChange`: A function called every time the content of the workspace changes.  It should take a single argument, which is the Blockly workspace object.  (You can call methods such as Blockly.JavaScript.workspaceToCode on this object.)
* `onXmlChange`: A function called every time the content of the workspace, debounced to be called at most once every 200 milliseconds.  This function should take a single argument, which is the new XML generated from the workspace.
* `onImportXmlError`: A function called if `initialXml` can't be imported.  This function takes a single argument, which is the error thrown during XML import.
* `onInject`: A function called after the Blockly workspace is injected.  This function takes a single argument, which is the newly-injected Blockly workspace object.  This is a good place to add Blockly plugins, if desired.
* `onDispose`: A function called after the Blockly workspace is disposed and removed from the page.  This function takes a single argument, which is the just-disposed Blockly workspace object.  Some Blockly plugins need to use this to dispose their own resources.

### useBlocklyWorkspace

`useBlocklyWorkspace(options) -> { workspace, xml }`;

#### Options object

All properties are optional, except `ref`, which must be passed.

* `ref`: **REQUIRED** A React ref object (created via `useRef`) pointing at an HTML element where Blockly will be injected.
* `initialXml`: The XML of the program to initially load in the editor.
* `workspaceConfiguration`: Any configuration options to be passed into `Blockly.inject` (except for `toolbox`, which is a separate prop).
* `toolboxConfiguration`: A JSON toolbox configuration (see [the Blockly documentation](https://developers.google.com/blockly/guides/configure/web/toolbox#json) for details on this format).
* `onWorkspaceChange`: A function called every time the content of the workspace changes.  It should take a single argument, which is the Blockly workspace object.  (You can call methods such as Blockly.JavaScript.workspaceToCode on this object.)
* `onImportXmlError`: A function called if `initialXml` can't be imported.  This function takes a single argument, which is the error thrown during XML import.
* `onInject`: A function called after the Blockly workspace is injected.  This function takes a single argument, which is the newly-injected Blockly workspace object.  This is a good place to add Blockly plugins, if desired.
* `onDispose`: A function called after the Blockly workspace is disposed and removed from the page.  This function takes a single argument, which is the just-disposed Blockly workspace object.  Some Blockly plugins need to use this to dispose their own resources.

#### Hook return value

`useBlocklyWorkspace` returns an object containing:

* `workspace`: The Blockly workspace object
* `xml`: XML generated from the content of the Blockly workspace, debounced to update at most once every 200 milliseconds

## Developer setup for working on this package

Clone this repository, and then inside it, do:

```bash
yarn install
yarn run start
```

webpack-dev-server will start and will be serving a demo of react-blockly, which should automatically refresh if you change the code locally.

## Example usage

See `public/index.html` and `src/dev-index.jsx` for a fairly full-fledged demo that shows off most of the features of this component.

## Contributing

We accept pull requests and issues!  You can file an issue on this project, or fork, modify, and file a pull request.

Please note that this project is released with a Contributor Code of Conduct. By participating in this project you agree to abide by its terms.

## Licensing

react-blockly is Copyright &copy; 2019-2021 Nat Budin.  Distributed under the terms and conditions of the MIT License.

react-blockly is based on react-blockly-component, which is Copyright &copy; 2015 PatientsLikeMe, Inc.  Distributed under the terms and conditions of the MIT License.
