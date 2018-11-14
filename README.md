# Problem with vue2-google-maps nuxt.js integration

This repo is a demo of an issue that occurs when you try to use [vue2-google-maps](<https://www.npmjs.com/package/vue2-google-maps>) with [nuxt.js](<https://nuxtjs.org>) 'out-of-the-box'.

## Steps to recreate

1. Install nuxt.js with npx
2. Say yes to express, vuetify, univeral, axios, eslint, prettier, npm.
3. Add a plugin file in the plugins directory (`gmaps.js`), as per [docs](<https://github.com/xkjyeah/vue-google-maps#quickstart-webpack-nuxt>) (see below*)
4. Add entries to the nuxt.config.js:

line 62:

```javascript
vendor: ['babel-polyfill', 'vue2-google-maps'],
```

line 41:

```javascript
plugins: ['@/plugins/vuetify', '@/plugins/gmaps'],
```

inside the build block extend function (line 46):

```javascript
      //add for vue2-google-maps
      if (!ctx.isClient) {
        // This instructs Webpack to include `vue2-google-maps`'s Vue files
        // for server-side rendering
        config.externals.splice(0, 0, function(context, request, callback) {
          if (/^vue2-google-maps($|\/)/.test(request)) {
            callback(null, false)
          } else {
            callback()
          }
        })
      }
```

*Plugin File:

```javascript
import Vue from 'vue'
import * as VueGoogleMaps from 'vue2-google-maps'

Vue.use(VueGoogleMaps, {
  load: {
    key: 'YOUR_API_TOKEN',
    libraries: 'places' // , // This is required if you use the Autocomplete plugin
    // OR: libraries: 'places,drawing'
    // OR: libraries: 'places,drawing,visualization'
    // (as you require)

    //// If you want to set the version, you can do so:
    // v: '3.26',
  } // ,

  //// If you intend to programmatically custom event listener code
  //// (e.g. `this.$refs.gmap.$on('zoom_changed', someFunc)`)
  //// instead of going through Vue templates (e.g. `<GmapMap @zoom_changed="someFunc">`)
  //// you might need to turn this on.
  // autobindAllEvents: false,

  //// If you want to manually install components, e.g.
  //// import {GmapMarker} from 'vue2-google-maps/src/components/marker'
  //// Vue.component('GmapMarker', GmapMarker)
  //// then disable the following:
  // installComponents: true,
})
```

## Error

```bash
(node:3042) UnhandledPromiseRejectionWarning: TypeError: Cannot read property 'splice' of undefined
    at Builder.extend (/Users/jamessherry/sites/nuxt-the-jump/nuxt.config.js:189:26)
    at Builder.<anonymous> (/Users/jamessherry/sites/nuxt-the-jump/node_modules/nuxt/dist/nuxt.js:160:27)
    at WebpackServerConfig.extendConfig (/Users/jamessherry/sites/nuxt-the-jump/node_modules/nuxt/dist/nuxt.js:3144:56)
    at WebpackServerConfig.config (/Users/jamessherry/sites/nuxt-the-jump/node_modules/nuxt/dist/nuxt.js:3182:33)
    at WebpackServerConfig.config (/Users/jamessherry/sites/nuxt-the-jump/node_modules/nuxt/dist/nuxt.js:3458:26)
    at Builder.webpackBuild (/Users/jamessherry/sites/nuxt-the-jump/node_modules/nuxt/dist/nuxt.js:3928:52)
    at Builder.build (/Users/jamessherry/sites/nuxt-the-jump/node_modules/nuxt/dist/nuxt.js:3632:16)
(node:3042) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). (rejection id: 2)
(node:3042) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.
[nodemon] clean exit - waiting for changes before restart
```

Logging shows that `config.externals` is undefined.
