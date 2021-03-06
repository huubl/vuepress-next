# vuepress-next

[![github check](https://github.com/vuepress/vuepress-next/workflows/check/badge.svg)](https://github.com/vuepress/vuepress-next/actions?query=workflow%3Acheck)
[![github docs](https://github.com/vuepress/vuepress-next/workflows/docs/badge.svg)](https://github.com/vuepress/vuepress-next/actions?query=workflow%3Adocs)
[![npm](https://badgen.net/npm/v/vuepress/next)](https://www.npmjs.com/package/vuepress)
[![license](https://badgen.net/github/license/vuepress/vuepress-next)](https://github.com/vuepress/vuepress-next/blob/main/LICENSE)

## Status: Beta

The codebase has been completely refactored with TypeScript. Some major changes:

- Fully migrated to Vue 3
- Extract `@vuepress/cli` from `vuepress` package
- Extract `@vuepress/client` from `@vuepress/core` package
- Extract `@vuepress/plugin-palette` from `@vuepress/core` package - stylus is no longer the default CSS pre-processor, and the way of styles customization should be determined by theme
- Extract `@vuepress/bundler-webpack` from `@vuepress/core` package and migrate to webpack 5
- Provide `@vuepress/bundler-vite` for [Vite](https://vitejs.dev/) support

The migration guide has not finished yet. For now you can check out the breaking changes list below as reference.

## Documentation

https://vuepress2.netlify.app

## Contribution

See [Contributing Guide](https://github.com/vuepress/vuepress-next/blob/main/docs/contributing.md).

## Breaking Changes

Temporarily record some breaking changes here.

### Core

#### User config

- `shouldPrefetch` -> the default value is changed to `false`
- `patterns` -> `pagePatterns`
- `extraWatchFiles` -> removed, watch files manually in `onWatched` hook
- `evergreen` -> the default value is changed to `true`
- `markdown`
  - `markdown.lineNumbers` -> `markdown.code.lineNumbers`, and the default value is changed to `true`
  - `markdown.slugify` -> removed
  - `markdown.pageSuffix` -> removed
  - `markdown.externalLinks` -> `markdown.links.externalAttrs`
  - `markdown.toc` -> changed
  - `markdown.plugins` -> removed
  - `markdown.extendMarkdown` -> removed
  - `markdown.extractHeaders` -> changed
- All webpack related configs are moved to `bundlerConfig` (with `@vuepress/bundler-webpack`)
  - `postcss` -> `bundlerConfig.postcss`
  - `stylus` -> `bundlerConfig.stylus`
  - `scss` -> `bundlerConfig.scss`
  - `sass` -> `bundlerConfig.sass`
  - `less` -> `bundlerConfig.less`
  - `chainWebpack` -> `bundlerConfig.chainWebpack`
  - `configureWebpack` -> `bundlerConfig.configureWebpack`

#### Conventional files and directories

- `.vuepress/enhanceApp.js` -> `.vuepress/clientAppEnhance.{js,ts}`
- `.vuepress/components` -> will not auto register components, and you need to register your components manually in `.vuepress/clientAppEnhance.{js,ts}`

#### Permalink patterns

- `:i_month` -> removed
- `:i_day` -> removed
- `:minutes` -> removed (undocumented in 1.0)
- `:seconds` -> removed (undocumented in 1.0)
- `:regular` -> `:raw`

#### Stylus Palette system

The stylus palette system of VuePress v1 (i.e. `styles/palette.styl` and `styles/index.styl`) is extracted to `@vuepress/plugin-palette`.

Theme authors can use their own way for users to configure styles (not be limited with stylus).

#### Frontmatter

- `meta` -> `head`, which uses the same type with `siteConfig.head`

  For example:
  
  ```yaml
  head:
    - - meta
      - name: foo
        content: bar
    - - link
      - rel: canonical
        href: foobar
    - - script
      - {}
      - console.log('hello from frontmatter');
  ```

  Has the same structure with:

  ```js
  // .vuepress/config.js
  module.exports = {
    // ...
    head: [
      ['meta', { name: 'foo', content: 'bar' }],
      ['link', { rel: 'canonical', href: 'foobar' }],
      ['script', {}, `console.log('hello from frontmatter');`],
    ],
    // ...
  }
  ```

#### Plugin API

- `ready` -> `onPrepared`
- `updated` -> `onWatched`
- `generated` -> `onGenerated`
- `additionalPages` -> removed, use `app.pages.push(createPage())` in `onInitialized` hook
- `clientDynamicModules` -> removed, use `app.writeTemp()` in `onPrepared` hook
- `enhanceAppFiles` -> `clientAppEnhanceFiles`
- `globalUIComponents` -> `clientAppRootComponentFiles`
- `clientRootMixin` -> `clientAppSetupFiles`
- `extendMarkdown` -> `extendsMarkdown`
- `chainMarkdown` -> removed
- `extendPageData` -> `extendsPageData`
- `extendsCli` -> removed
- `configureWebpack` -> removed
- `chainWebpack` -> removed
- `beforeDevServer` -> removed
- `afterDevServer` -> removed

#### Theme API

- `.vuepress/theme` -> won't be used as theme implicitly if the path exists, and set the path explicitly via `theme` option.

- `extend` -> `extends`

  You can still inherit a parent theme with `extends: 'parent-theme'`, which will extends the plugins, layouts, etc.

  However, the `@theme` and `@parent-theme` aliases are not available now.

### CLI

- `eject` command -> removed
- `-c, --cache [cache]` -> `--cache <cache>` - the shorthand `-c` is not for `cache` option, and the value of `cache` option is not optional
- `--no-cache` -> `--clean-cache`

### Default Theme

- `<CodeGroup />`, `<CodeBlock />` -> `<CodeGroup />`, `<CodeGroupItem />`
- `<Badge />`
  - `$badgeErrorColor` -> `$badgeDangerColor`
  - `type` prop only accepts `tip`, `warning` and `danger`
- Default theme config has changed a lot. Please refer to the documentation.
