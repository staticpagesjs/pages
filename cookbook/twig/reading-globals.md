# Twig – reading globals

```js
const fs = require('fs');
const path = require('path');
const yaml = require('js-yaml');
const { staticPages } = require('@static-pages/core');
const { markdownReader } = require('@static-pages/markdown-reader');
const { twigWriter } = require('@static-pages/twig-writer');

staticPages({
	from: markdownReader({
        cwd: 'pages',
        pattern: '**/*.md',
    }),
	to: twigWriter({
		globals: yaml.load(fs.readFileSync(path.resolve(__dirname, 'globals.yaml'), 'utf-8')),
		filters: {
			trans: [ // an example `'my-string'|trans` filter. uses the 'lang' property from the context to pick language
				(context, message) => {
					const langCode = context.get('lang') ?? 'en';
					if (!translations[langCode]) {
						const transFile = path.resolve(__dirname, 'messages', langCode + '.yaml');
						if (fs.existsSync(transFile)) {
							translations[langCode] = yaml.load(fs.readFileSync(transFile, 'utf-8'));
						} else {
							translations[langCode] = {};
						}
					}
					return translations[langCode][message] ?? message;
				},
				{ needs_context: true }
			],
		},
		functions: {
			yaml: [ // an example `yaml(...)` function which dumps the input data to yaml
				x => yaml.dump(x, {
                    replacer: (key, value) => {
	                    if (!Array.isArray(value) && typeof value?.entries === 'function') {
		                    return Object.fromEntries(value.entries());
	                    }
	                    return value;
                    }
                }),
				{ is_safe: ['html'] }
			],
		},
	}),
}).catch(err => {
    console.error(err?.message ?? err);
    process.exit(1);
});
```
