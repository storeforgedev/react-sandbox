> React component for "sandboxing" third party content within an iframe

[![NPM Version](https://img.shields.io/npm/v/%40storeforge%2Freact-sandbox)](https://npmjs.com/@storeforge/react-sandbox)
![NPM License](https://img.shields.io/npm/l/%40storeforge%2Freact-sandbox)
![NPM Downloads](https://img.shields.io/npm/dm/%40storeforge%2Freact-sandbox)

It is common for third party widgets to be distributed as a simple `<script>` tag, intended to be inserted somewhere on the page. These scripts often look for DOM nodes with particular ID's, and replace their content to mount widgets.

These types of widgets are problematic for React SPA frameworks, as often navigating between pages does not trigger a document load. This means that the third party script tags are not re-executed, and the injected widget content is lost when navigating.

Additionally, these third party scripts often use DOM manipulation to insert styles and other content into the page, which is not cleaned up when navigating an SPA. If you do manage to force the script to re-execute when navigating, chances are your DOM is gradually being polluted with more and more injected content left over from previous "pages".

This package introduces a `Sandbox` component, which is essentially just an `<iframe>` with the `srcDoc` attribute. Inserting widgets this way means the third party JavaScript runs in a separate browser context, preventing pollution of your SPA. When navigating away and the iframe is unmounted, all the content within will be cleaned up by the browser.

The `Sandbox` component automatically injects some JavaScript to efficiently resize the iframe when the height of the content changes. This uses [MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver), rather than polling with a `setInternal` like lots of similar solutions use.

## Install

```sh
npm install @storeforgedev/react-sandbox
```

## Usage

Take the third party code they are asking you to drop into the site, and instead of adding it directly to your React component, format it as a string, and pass it to the `Sandbox` component.

It is recommended to add a `key` prop to the component, as the content you pass to the child will only be rendered once. If you want to render something different, changing the `key` prop will force the iframe to properly unmount, and mount with the new content, thus re-executing scripts.

```tsx
import { Sandbox } from "@storeforgedev/react-sandbox";

return (
  <Sandbox key="unique">
    {`<script src="https://example.com/script.js"></script>`}
  </Sandbox>
);
```

## Adding content to `<head>`

If you need to add content to the `<head>` element in the sandbox, use the `headContent` prop.

```tsx
import { Sandbox } from "@storeforgedev/react-sandbox";

const headContent = [`<link href="/style.css" rel="stylesheet" />`].join("\n");

return (
  <Sandbox key="unique" headContent={headContent}>
    {`<script src="https://example.com/script.js"></script>`}
  </Sandbox>
);
```
