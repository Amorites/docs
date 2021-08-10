---
id: export
title: Exporting
---

A scope is a set of components, usually they are semantically related. Exporting components to remote scopes makes them available for other developers to consume. You can create remote scopes on [your own server](/docs/bit-server) or manage components' scopes the [bit.dev](https://bit.dev) components hub.

Log into an account on `bit.dev` to create private or public scopes.

To define your own remote scopes follow [these steps](/docs/bit-server#working-with-remote-scopes).

Only components that are tagged can be exported. The tagged version is exported and resides on the remote scope. Use the [bit export](/docs/apis/cli-all#export) command to export components.

## Remote Scopes

You can store components in any number of remote scopes.  
When running `bit export` with no arguments, Bit attempts exporting to the original scope (the one you imported the components from). So, if you imported a component from a scope where you have no write access on, it will throw authentication error.

To export all staged components to a single scope or scope, specify a remote scope as a destination:

```shell
$ bit export user.my-scope
2 components were exported to scope owner/my-scope
```

Bit exports all staged components to the `owner/my-scope` scope.

To export specific components to a remote scope add the component ID to the export command:

```shell
$ bit export bit.movie-app hello/world
component hello/world was exported to scope bit/movie-app
```

You can also use wildcards to export multiple components:

```shell
bit export owner.my-scope owner.my-scope/*
# depending on your OS, you may need to wrap the component path with quotes
```

## Ejecting components

Components exported to Bit are consumable with package managers. Bit allows ejecting components and replaces them with its node module. The name of this process is 'ejecting'. This is sugar syntax for both deleting the local component and running `npm install`.

To eject a component on export, use [bit export](/docs/apis/cli-all#export) with the `--eject` option:

```shell
$ bit export bit.movie-app --eject
2 components were exported to scope bit/movie-app
```

To eject a component after export, use [bit eject](/docs/apis/cli-all#eject) command:

```shell
bit eject hello/world
```

## Multiple Remote Scopes

EXPERIMENTAL

When a component is exported, you specify the remote scope to export:

```shell
bit export owner.my-scope my-component
```

For a component that is exported for the first time, Bit uses the [defaultScope](/docs/conf-bit-json#defaultscope_) configuration, if exists, to export to that scope.

This scope is considered as the primary scope of the component.

If you specify a different scope on the initial export, this scope is set as the primary scope.

You can see the scope of the component when running `bit list`. The scope is shown with the component name.

### Export to Additional Scopes

You can export the same component to additional scopes which are different from the primary scope by specifying:

```shell
bit export owner.other-scope my-component
```

> When a component is exported to a scope which is different from the primary scope, the component remains in staging mode. Only when exported to the primary scope, it will become exported.

You can change the component primary scope when exporting to another scope by using the `--set-current-scope` during the import.

When exporting to another scope, you can also export with it all the components that are dependencies of this components with a single command:

```shell
bit export owner.other-scope my-component --include-dependencies
```

All the components that are dependencies, are also exported to the other scope. However, the component still points to the components on their original scope as the dependencies. E.g.  
`scope1.compA` depends on `scope1.compB`.

If you run `bit export scope2.compA --include-dependencies`, 2 components are created in scope2:  
`scope2.compA` and `scope2.compB`. However, `scope2.compA` still has `scope1.compB` as its dependency. To change scope2.compA to have `scope2.compB` as its dependency run:

```shell
bit export owner.other-scope my-component --include-dependencies --rewire
```

Now in scope2 you have `scope2.compA` that depends on `scope2.compB`.
