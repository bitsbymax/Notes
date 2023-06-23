# Micro-FE  

Micro services for the backend === micro-frontend for the client.

## Atomic design methodology

Atoms -> Molecules -> Organisms -> Templates -> Pages
Most appropriate for sharing as micro-fe are _Organisms_.

## Module Federation

It is a feature that is built in Webpack 5. Can be applied to any popular framework/library.
Approach when micro-fes shares componenst, functions or anu piece of JS between each other directly.
It is also possible to use this approach together with come npm library, for example, as fallback in case of an error. Simplier version of error handling is Error Boundary or just Suspense.

Use webpack.config.js to configure module federation
