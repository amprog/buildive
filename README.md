# Buildive: An Interactive Build Template

### Getting Started
A dead simple template for building interactives. All build tasks are handled with [npm](https://github.com/npm/npm) scripts inside `package.json`. Source files are kept in src/ while build files are kept in public/. To keep things tidy and modular, this template is designed with [Browserify](https://github.com/substack/node-browserify#usage), [Sass](https://github.com/sass/sass) and [Mustache](https://github.com/janl/mustache.js/) in mind and has a directory structure meant to reinforce modular code.

### Build Setup
- make sure you have [node and the latest npm](https://docs.npmjs.com/getting-started/installing-node) installed
- clone this repo: `git clone https://github.com/amprog/buildive.git`
- define the following as environment variables `nano ~/.bash_profile` (you'll need to ge the RSA KEY, username and state IP address from webtech):
```
export RSA_KEY="[path/to/rsa/key]"
export STAGE_NAME="[username for servers]"
export STAGE_IP="[IP address of staging server]"
```
- Install all development dependencies globally on machine to make their CLI available: `npm install -g browserify uglify-js watchify minifier node-sass http-server livereload watch`
- Set up bash build script for convenience: `cp buildive /usr/local/bin/`

### Project Setup
- run `buildive <interactive_name>`
- Open `package.json` and change name, description, keyword, staging directory
- Edit the `meta` object in `package.json`. This data is used in the default `header.mustache` template.
- run `npm install` if you did not install development dependencies globally

### Develop
- Install project dependencies (e.g. `npm install d3 react underscore --save`)
- Start local server / livereload : `npm start`
- Use [CommonJS](http://requirejs.org/docs/commonjs.html) syntax to import scripts into `src/js/index.js`
- @import .scss files into `src/sass/index.scss`
- Store mustache partials in `partials` directory. Using mustache's partials syntax (`{{> partial_name }}`), add partials to `src/views/index.mustache` (note: partials must have a unique filenames across `partials/` and have .mustache extension)

### Build
- for production-ready builds: `npm run build`. This will minify your javascript and css and swap out .js/.css references in public/index.html for .min.js and .min.css references.

### Push Updates

- for testing and sharing internally, use npm `preview` scripts which copy files to `/preview/2016/` on staging server, outside of the git working tree: `npm run preview:push`
  - *note:* the preview scripts maintain versions (e.g `/previews/2016/buildive/v1.0.1/`)
- when ready to push to production after building: `npm run stage:push`

The current process for going live with interactives is to load files to the staging server (you can do this manually via FTP using CyberDuck or Filezilla, if you prefer or just run `npm run stage:push`), then adding the changes to the git repo on the staging server (`npm run stage:commit` or ssh into staging server and add it manually with git). You then put in a request to webtech via Slack, and someone on the team will pull down your changes onto the production interactive server.

## Interactive styling

- Interactive markup should go inside `<main></main>` which has a max 880px width to fit CAP's wide-format wordpress post
- If you need a full-width header, use `<header></header>`.
- by default, direct children of header will be floated
- add a logo with `<div class="logo"></div>`
- a direct child of main or header with a `wrap` class will get 10px left/right padding and remove any floats.
- for titles, use `<h1 class="hed"></h1>` and `<h2 class="dek"></h2>`
- if you don't want to use default CAP styles, remove `modules/core/style` import from index.scss
- most interactives use [Adobe's Typekit](https://typekit.com/). Headers use [Adelle Condensed](https://typekit.com/fonts/adelle-condensed) and body uses [Myriad Pro Semi Condensed](https://typekit.com/fonts/myriad-pro-semi-condensed).

### Sass helpers

|  name | use |
|---|---|
| @function color( $CAP-color, $opacity*:optional*)  | Use CAP palettes for red, blue, brown, yellow, orange and gray in dark, medium, light, ultra-light (e.g.: `box-shadow: 0 0 1px color( ultra-light-blue, 0.75 );`)|
| @mixin vertical-center  |  vertically center element (inside relative positioned parent) |
| @mixin horizontal-center | horizontally center element (inside relative positioned parent)  |
| @mixin center | center elment horizontally and vertically (inside relative positioned parent) |
| @mixin bottom-box-border($thickness*:integer*, $color*:optional*) | add border without changing box-sizing |  
| @mixin top-box-border($thickness*:integer*, $color*:optional*) | add border without changing box-sizing |
| @mixin transition($property,$duration, $transition) | convenience mixin for cross-browser support |
| @mixin rotate($deg) | convenience mixin for cross-browser support |
| @mixin animation-optimization | add translateZ(0) to elements for faster GPU rendering on  mobile devices |
| @mixin light-box-shadow($border-color*:optional*) | a simple, subtle box shadow |

## NPM scripts

|  command | use |
|---|---|
| `npm start` | watch files, livereload, start server at `public/`. Does not minify. |
| `npm run connect` | start server at `public/` |
| `npm run build` | for production-ready code, adds minification |
| `npm run preview:push` | copies `public/` to staging server to a folder in `interactives/preview/2016/v<version>/` with the package's name, to use a different directory, change the `previewDir` config in `package.json` (must have RSA_KEY, STAGE_NAME, and STAGE_IP defined as environment variables ) |
| `npm run preview:push:patch` | same as preview:push except runs `npm version patch` |
| `npm run preview:open` | open most recent preview in browser |
| `npm run stage:connect` | connect to staging server via ssh (must have RSA_KEY, STAGE_NAME, and STAGE_IP defined as environment variables ) |
| `npm run stage:push` | push update to staging directory, overwrites old files (default: projects/2016 ) |
| `npm run stage:commit` | commit staging changes to git |
| `npm run stage:open` | preview stage in browser |
| `npm run watch` | watch js and sass files |
| `npm run browserify` | build js files |
| `npm run sass` | build sass files |
| `npm run minify` | minify js and sass files |
| `npm run mustache` | Mustache's CLI doesn't add partials recursively. Hack to retrieve all partials, insert package.json's meta data and render HTML to `public/index.html`  |
| `npm run mini:add` | Swap references to app.js/app.css for app.min.js/app.min.css in public/index.html |


## Spitshine

Make sure you get your meta tags right. `package.json` has a few properties that will fill in the meta data for the image, title and description tags for (Open Graph)[http://ogp.me/]. Don't forget to snap a nice screenshot of your interactive and save it as /public/images/card.png/

## Troubleshooting

if npm is giving you trouble, run `npm update`. Still trouble? `npm install`. Still giving you hell? remove the `node_modules` directory and `npm install`. Still?? You sure you have the latest version of npm? `sudo npm install npm -g`

Sometimes the livereload server refuses to shut down. You can either remove the livereload script from package.json, or shut down the livereload server manually when it acts up. In the terminal, run `lsof -n -i4TCP:35729`. Which outputs:
```
COMMAND     PID  ...
node      27594  ...
```
kill it: `kill 27594`
