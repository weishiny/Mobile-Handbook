## Karma Configuration

In order to serve you well, Karma needs to know about your project in order to test it and this is done via a configuration file. This page explains how to create such a configuration file.

### Generating the config file

The configuration file can be generated using `karma init`:

```
$ karma init my.conf.js

Which testing framework do you want to use?
Press tab to list possible options. Enter to move to the next question.
> jasmine

Do you want to use Require.js?
This will add Require.js plugin.
Press tab to list possible options. Enter to move to the next question.
> no

Do you want to capture a browser automatically?
Press tab to list possible options. Enter empty string to move to the next question.
> Chrome
> Firefox
>

What is the location of your source and test files?
You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
Press Enter to move to the next question.
> *.js
> test/**/*.js
>

Should any of the files included by the previous patterns be excluded?
You can use glob patterns, eg. "**/*.swp".
Press Enter to move to the next question.
>

Do you want Karma to watch all the files and run the tests on change?
Press tab to list possible options.
> yes

Config file generated at "/Users/vojta/Code/karma/my.conf.js".
```

### Starting Karma

When starting Karma, the configuration file path can be passed in as the first argument.
By default, Karma will look for `karma.conf.js` or `karma.conf.coffee` in the current directory.

```
# Start Karma using your configuration:
$ karma start my.conf.js
```

:sparkles::collision: There are so many configuration inside `karma.conf.js`, so we see the `karma.conf.js` in **angular2** project.

*karma.conf.js*
```javascript

module.exports = function(config) {

  var appBase    = 'src/';       // transpiled app JS and map files
  var appSrcBase = appBase;      // app source TS files

  // Testing helpers (optional) are conventionally in a folder called `testing`
  var testingBase    = 'src/'; // transpiled test JS and map files
  var testingSrcBase = 'src/'; // test source TS files

  config.set({
    basePath: '',
    frameworks: ['jasmine'],

    plugins: [
      require('karma-jasmine'),
      require('karma-chrome-launcher'),
      require('karma-jasmine-html-reporter')
    ],

    client: {
      builtPaths: [appBase, testingBase], // add more spec base paths as needed
      clearContext: false // leave Jasmine Spec Runner output visible in browser
    },

    customLaunchers: {
      // From the CLI. Not used here but interesting
      // chrome setup for travis CI using chromium
      Chrome_travis_ci: {
        base: 'Chrome',
        flags: ['--no-sandbox']
      }
    },

    files: [
      // System.js for module loading
      'node_modules/systemjs/dist/system.src.js',

      // Polyfills
      'node_modules/core-js/client/shim.js',

      // zone.js
      'node_modules/zone.js/dist/zone.js',
      'node_modules/zone.js/dist/long-stack-trace-zone.js',
      'node_modules/zone.js/dist/proxy.js',
      'node_modules/zone.js/dist/sync-test.js',
      'node_modules/zone.js/dist/jasmine-patch.js',
      'node_modules/zone.js/dist/async-test.js',
      'node_modules/zone.js/dist/fake-async-test.js',

      // RxJs
      { pattern: 'node_modules/rxjs/**/*.js', included: false, watched: false },
      { pattern: 'node_modules/rxjs/**/*.js.map', included: false, watched: false },

      // Paths loaded via module imports:
      // Angular itself
      { pattern: 'node_modules/@angular/**/*.js', included: false, watched: false },
      { pattern: 'node_modules/@angular/**/*.js.map', included: false, watched: false },

      { pattern: appBase + '/systemjs.config.js', included: false, watched: false },
      { pattern: appBase + '/systemjs.config.extras.js', included: false, watched: false },
      'karma-test-shim.js', // optionally extend SystemJS mapping e.g., with barrels

      // transpiled application & spec code paths loaded via module imports
      { pattern: appBase + '**/*.js', included: false, watched: true },
      { pattern: testingBase + '**/*.js', included: false, watched: true },


      // Asset (HTML & CSS) paths loaded via Angular's component compiler
      // (these paths need to be rewritten, see proxies section)
      { pattern: appBase + '**/*.html', included: false, watched: true },
      { pattern: appBase + '**/*.css', included: false, watched: true },

      // Paths for debugging with source maps in dev tools
      { pattern: appBase + '**/*.ts', included: false, watched: false },
      { pattern: appBase + '**/*.js.map', included: false, watched: false },
      { pattern: testingSrcBase + '**/*.ts', included: false, watched: false },
      { pattern: testingBase + '**/*.js.map', included: false, watched: false}
    ],

    // Proxied base paths for loading assets
    proxies: {
      // required for modules fetched by SystemJS
      '/base/src/node_modules/': '/base/node_modules/'
    },

    exclude: [],
    preprocessors: {},
    reporters: ['progress', 'kjhtml'],

    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ['Chrome'],
    singleRun: false
  })
}

```

:mag_right: please refer to [Karma Configuration](http://karma-runner.github.io/1.0/config/configuration-file.html)

