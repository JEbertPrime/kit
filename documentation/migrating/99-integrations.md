---
title: Integrations
---

See [the FAQ](/faq#integrations) for detailed information about integrations.

### HTML minifier

Sapper includes `html-minifier` by default. SvelteKit does not include this, but it can be added as a [hook](/docs#hooks-handle):

```js
import { minify } from 'html-minifier';
import { prerendering } from '$app/env';

const minification_options = {
	collapseBooleanAttributes: true,
	collapseWhitespace: true,
	conservativeCollapse: true,
	decodeEntities: true,
	html5: true,
	ignoreCustomComments: [/^#/],
	minifyCSS: true,
	minifyJS: false,
	removeAttributeQuotes: true,
	removeComments: true,
	removeOptionalTags: true,
	removeRedundantAttributes: true,
	removeScriptTypeAttributes: true,
	removeStyleLinkTypeAttributes: true,
	sortAttributes: true,
	sortClassName: true
};

export async function handle({ event, resolve }) {
  const response = await resolve(event);

  if (prerendering && response.headers.get('content-type') === 'text/html') {
    return new Response(minify(await response.text(), minification_options), {
    	status: response.status,
    	headers: response.headers
    });
  }

  return response;
}
```

Note that `prerendering` is `false` when using `svelte-kit preview` to test the production build of the site, so to verify the results of minifying, you'll need to inspect the built HTML files directly.
