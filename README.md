# Package "name" Resolution Proposal

Node.js proposal to support resolving any package by its own package.json _"name"_ property from within the package, to avoid the constant need for backtracking requires.

### Background

Node.js `--experimental modules` now supports a concept of package scope where loading a _".js"_ module checks recursively for the parent folder package.json file to see if a _"type": "module"_ scope has been provided.

Using this same package scope principle we can also check the package.json "name" for any module within a package allowing support for resolving the package's own name as a simple way to solve the backtracking import problem.

### Example

Consider the following file structure:

```
app/package.json
app/featureA/sub/module.js
app/featureB/module.js
```

Typically, `app/featureA/sub/module.js` would load `app/featureB/module.js` with the following:

```js
import componentB from '../../featureB/module.js';
```

With the package.json "name" field, we could set `app/package.json`
to contain:

```json
{
  "name": "app"
}
```

this would then allow us to write `app/featureA/sub/module.js` as:

```js
import app from 'app/featureB/module.js';
```

### Portability

A key feature of this proposal is package portability.

That is, publishing the package to npm and having it installed in node_modules continues to support this feature just fine as the package scope applies just the same in node_modules, without any further configuration.

Even if the package alias (directory name in node_modules) is different, that doesn't affect the package.json _"name"_ field still being the original name, that will continue to work in the scope just fine.

The cost here is that the value of _"name"_ gets reserved in the resolver within the package, and cannot resolve to any other dependencies other than the package itself.

### Alternative Approaches

There could be alternatives to using the package.json _"name"_ to be explored, such as having a unique name or symbol to indicate the current package.

For example:

```js
import app from '~/featureB/module.js';
```

### FAQ

#### What about tooling support?

This proposal is designed to apply to not just Node.js but also other tools so that support can be provided throughout the ecosystem for the conventions it specifies. That will take some time, but with Node.js leading the way it shouldn't take long for tooling to follow, since this provides a solution to a well-known problem.

#### Could this be supported for CommonJS?

That is still unclear. This proposal is currently only for the ES Module resolver, but support could be provided for the CommonJS resolver as well.

The concern here is that this might break existing packages that happen to have a _"dependencies"_ entry matching their own _"name"_ entry, so there are some backwards compatibility concerns here that would need to be worked out.

### Specification & Implementation

No specification or implementation work has begun yet.
