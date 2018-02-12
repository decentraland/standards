# Decentraland's TSLint rule set

**Document Status**: *Draft*.  
**Maintainer**: Agustin Mendez &lt;[agus@decentraland.org](mailto:agus@decentraland.org)&gt;.

## Introduction

This document describes the process to set up the Decentraland's rules for `tslint`

## Linter choice 

We choose `tslint` because it has a familiar set of rules with `eslint`. 
It also has a [language service plugin](https://github.com/angelozerr/tslint-language-service),
this is important to support other editors and IDE than VSCode, since they only need to hook to the Typescript language server.

## Installation

Assuming Typescript is already installed, to fully set up the linter, the project will require the following packages

```bash
npm install --save-dev \
  dcl-tslint-config-standard \
  tslint-language-service \
  tslint
```

In your project root you need a file called `tslint.json` with this content:

```yaml
{
  "extends": "dcl-tslint-config-standard"
}
```

Inside the `tsconfig.json` you need to enable the plugin `tslint-language-service` by adding:

```yaml
{
  "compilerOptions": {
    "plugins": [
      { "name": "tslint-language-service"}
    ]
  }
}
```

## Related Documents

To take a deep dive into our code conventions for React/Redux refer to [this document](react-redux.md).
