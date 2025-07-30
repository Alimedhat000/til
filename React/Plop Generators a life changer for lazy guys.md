Today I learned how Plop can take the wheel and save me from the repetitive misery of creating boilerplate files manually.

A Plop generator file is simply a function that returns an object with three keys:

```js
module.exports = function (plop) {
  return {
    description: 'What this generator does', // A simple descreption 
    prompts: [...], // What questions to ask the user
    actions: [...], // What files to create or modify
  };
};
```

This is the core structure of any Plop generator.

- `prompts` usually go like this

```js
prompts: [
  {
    type: 'input',
    name: 'name',
    message: 'Component name (PascalCase)',
  },
  {
    type: 'list',
    name: 'feature',
    message: 'Which feature does this component belong to?',
    choices: ['components', 'auth', 'dashboard'],
  },
]
```

and each answer from these will be passed to `actions`

- `actions` is where you handle file creation 

```js
{
  type: 'add', // or 'modify', 'append', etc.
  path: 'src/components/{{pascalCase name}}/index.ts',
  templateFile: 'generators/component/index.ts.hbs',
  data: {
  passedData: 'lolcat'
  }
}
```

That data will be available inside the template as `{{passedData}}`.

### Templates:

Plop uses **Handlebars (.hbs)** templates for generating file contents.

Example (`component.tsx.hbs`):

```tsx
import React from 'react';

export function {{pascalCase name}}() {
  return <div>{{pascalCase name}} component</div>;
}
```

When the user enters `MyChart` as the component name, this becomes:

```tsx
import React from 'react';

export function MyChart() {
   return <div>MyChart component</div>;
}
```