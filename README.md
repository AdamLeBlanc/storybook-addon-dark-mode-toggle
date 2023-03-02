# Storybook Addon theme-toggle

Simple addon that allows you to change a `data-*` value on a dom node of your choice.

If you have something in your codebase like this ->

```scss
html[data-theme="dark"] {
  @include darkTheme;
}
```

Than this is the plugin for you.

## Setup

1. `yarn add -D storybook-addon-dark-mode-toggle` or `npm install --save-dev storybook-addon-dark-mode-toggle`
2. `.storybook/main.js` -> `addons: [..others, 'storybook-addon-dark-mode-toggle']`,
3. OPTIONAL: Add params to `preview.js` under the key `data-theme-toggle`
4. import whatever `.[s]css` files control your theme into `.storybook/preview.js`, such as `import '../styles/theme/theme.scss';` (see motivation for what this file may look like.)
5. Click the added icon to toggle theme

## Configuration

Optional, but if you choose to add a configuration, you can't skip any of the fields.

You can configure the addon by adding the following to `.storybook/preview.js` (default values shown):

```javascript
export const parameters = {
    'data-theme-toggle': {
    querySelector: "html",
    "data-target": "theme",
    default: "light",
    values: {
      dark: "dark",
      light: "light",
    },
    lightFill: "#a05b00",
    darkFill: "#0926b5",
  }  
};
```

- `querySelector`: value that will be passed into a `document.querySelector('selector')` call to get the dom element your data attribute will be applied to.
- `data-target`: the name of the data attribute that will be set. The above default would give you `data-theme='light'`
- `default`: either `dark` or `light`, will set the initial data attribute value based on `values`
- `values`: the values that the data attribute will be set to for dark/light themes
- `lightFill`: The fill color for the addons toolbar icon in light mode
- `darkFill`: The fill color for the addons toolbar icon in dark mode

## Motivation For Making this Addon

I do my themes by setting default theme variables in a `:root {...}` selector and then controlling the theme by overwriting those variables in other selectors. All of my projects have a `theme.scss` file like this ->

```scss
@use "./theme-light.scss" as *;
@use "./theme-dark.scss" as *;

@import "./tokens.scss";

:root {
  @include lightTheme;
}

@media (prefers-color-scheme: dark) {
  :root {
    @include darkTheme;
  }
}

html[data-theme="light"] {
  @include lightTheme;
}

html[data-theme="dark"] {
  @include darkTheme;
}
```

Where `tokens.scss` has the "global" custom css properties (variables) that are referenced in my theme files, `theme-light.scss` and `theme-dark.scss` (similar to how tokens work in material 3). I then default to the light theme but respect the users preference for a dark theme with `@media (prefers-color-scheme: dark)`.

I also allow the user to toggle between light and dark themes via a `data-theme` selector on the `html` tag.

I was unable to find a suitable addon that would allow me to quickly toggle the theme my components are rendered with in storybook. There were a few addons that were on the right track, notably [storybook-theme-toggle](https://github.com/antonis-zisis/storybook-theme-toggle) by [Antonis Zisis](https://github.com/antonis-zisis). However, they lacked support for configuration and couldn't be dropped into my project without changing my source code.

Therefore I set out to create a simple toggle plugin that can do the same thing as `storybook-theme-toggle` but with optional configuration.

### Addon Development docs

### Development scripts

- `yarn start` runs babel in watch mode and starts Storybook
- `yarn build` build and package your addon code

### Metadata

Storybook addons are listed in the [catalog](https://storybook.js.org/addons) and distributed via npm. The catalog is populated by querying npm's registry for Storybook-specific metadata in `package.json`. This project has been configured with sample data. Learn more about available options in the [Addon metadata docs](https://storybook.js.org/docs/react/addons/addon-catalog#addon-metadata).

## Release Management

### Setup

This project is configured to use [auto](https://github.com/intuit/auto) for release management. It generates a changelog and pushes it to both GitHub and npm. Therefore, you need to configure access to both:

- [`NPM_TOKEN`](https://docs.npmjs.com/creating-and-viewing-access-tokens#creating-access-tokens) Create a token with both _Read and Publish_ permissions.
- [`GH_TOKEN`](https://github.com/settings/tokens) Create a token with the `repo` scope.

Then open your `package.json` and edit the following fields:

- `name`
- `author`
- `repository`

#### Local

To use `auto` locally create a `.env` file at the root of your project and add your tokens to it:

```bash
GH_TOKEN=<value you just got from GitHub>
NPM_TOKEN=<value you just got from npm>
```

Lastly, **create labels on GitHub**. You’ll use these labels in the future when making changes to the package.

```bash
npx auto create-labels
```

If you check on GitHub, you’ll now see a set of labels that `auto` would like you to use. Use these to tag future pull requests.

#### GitHub Actions

This template comes with GitHub actions already set up to publish your addon anytime someone pushes to your repository.

Go to `Settings > Secrets`, click `New repository secret`, and add your `NPM_TOKEN`.

### Creating a release

To create a release locally you can run the following command, otherwise the GitHub action will make the release for you.

```sh
yarn release
```

That will:

- Build and package the addon code
- Bump the version
- Push a release to GitHub and npm
- Push a changelog to GitHub
