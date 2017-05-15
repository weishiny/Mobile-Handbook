# Unit Test

## ToDo

- [x] Jasmine Introduction
- [x] Jasmine Syntax Introduction
- [x] Karma Introduction
- [x] Karma Configuration Introduction
- [ ] Introduction of Angular2 integrated with Jasmine and Karma

## Jasmine (test framework)

Jasmine is a behavior-driven development framework for testing JavaScript code. It does not depend on any other JavaScript frameworks. It does not require a DOM. And it has a clean, obvious syntax so that you can easily write tests.

In the first place, We can use `Standalone Distribution`

You can download the standalone distribution, which contains everything you need to start running Jasmine. After downloading a particular version and unzipping, opening `SpecRunner.html` will run the included specs. You'll note that both the source files and their respective specs are linked in the <head> of the SpecRunner.html. To start using Jasmine, replace the source/spec files with your own.

## Karma (test runner)

Karma is not a testing framework, nor an assertion library. Karma just launches an HTTP server, and generates the test runner HTML file you probably already know from your favourite testing framework. So for testing purposes you can use pretty much anything you like. There are already plugins for most of the common testing frameworks:

- Jasmine
- Mocha
- QUnit
- and many others

When to use

- You want to test code in real browsers.
- You want to test code in multiple browsers (desktop, mobile, tablets, etc.).
- You want to execute your tests locally during development.
- You want to execute your tests on a continuous integration server.
- You want to execute your tests on every save.
- You love your terminal.
- You don't want your (testing) life to suck.
- You want to use Istanbul to automagically generate coverage reports.
- You want to use RequireJS for your source files.

---

下載Jasmine的Standalone Distribution版本,開啟`SpecRunner.html`,會發現到底 run Jasmine test 的環境是怎麼寫的.

```html

<html>
<head>
  <meta charset="utf-8">
  <title>Jasmine Spec Runner v2.6.1</title>

  <link rel="shortcut icon" type="image/png" href="lib/jasmine-2.6.1/jasmine_favicon.png">
  <link rel="stylesheet" href="lib/jasmine-2.6.1/jasmine.css">

  <script src="lib/jasmine-2.6.1/jasmine.js"></script>
  <script src="lib/jasmine-2.6.1/jasmine-html.js"></script>
  <script src="lib/jasmine-2.6.1/boot.js"></script>

  <!-- include source files here... -->
  <script src="src/Player.js"></script>
  <script src="src/Song.js"></script>

  <!-- include spec files here... -->
  <script src="spec/SpecHelper.js"></script>
  <script src="spec/PlayerSpec.js"></script>

</head>

<body>
</body>
</html>

```

再去看`lib/jasmine-2.6.1/boot.js`此檔案會發現run jasmine test機制如何運作

```javascript
/**
 Starting with version 2.0, this file "boots" Jasmine, performing all of the necessary initialization before executing the loaded environment and all of a project's specs. This file should be loaded after `jasmine.js` and `jasmine_html.js`, but before any project source files or spec files are loaded. Thus this file can also be used to customize Jasmine for a project.

 If a project is using Jasmine via the standalone distribution, this file can be customized directly. If a project is using Jasmine via the [Ruby gem][jasmine-gem], this file can be copied into the support directory via `jasmine copy_boot_js`. Other environments (e.g., Python) will have different mechanisms.

 The location of `boot.js` can be specified and/or overridden in `jasmine.yml`.

 [jasmine-gem]: http://github.com/pivotal/jasmine-gem
 */

(function() {

  /**
   * ## Require &amp; Instantiate
   *
   * Require Jasmine's core files. Specifically, this requires and attaches all of Jasmine's code to the `jasmine` reference.
   */
  window.jasmine = jasmineRequire.core(jasmineRequire);

  /**
   * Since this is being run in a browser and the results should populate to an HTML page, require the HTML-specific Jasmine code, injecting the same reference.
   */
  jasmineRequire.html(jasmine);

  /**
   * Create the Jasmine environment. This is used to run all specs in a project.
   */
  var env = jasmine.getEnv();

  /**
   * ## The Global Interface
   *
   * Build up the functions that will be exposed as the Jasmine public interface. A project can customize, rename or alias any of these functions as desired, provided the implementation remains unchanged.
   */
  var jasmineInterface = jasmineRequire.interface(jasmine, env);

  /**
   * Add all of the Jasmine global/public interface to the global scope, so a project can use the public interface directly. For example, calling `describe` in specs instead of `jasmine.getEnv().describe`.
   */
  extend(window, jasmineInterface);

  /**
   * ## Runner Parameters
   *
   * More browser specific code - wrap the query string in an object and to allow for getting/setting parameters from the runner user interface.
   */

  var queryString = new jasmine.QueryString({
    getWindowLocation: function() { return window.location; }
  });

  var filterSpecs = !!queryString.getParam("spec");

  var catchingExceptions = queryString.getParam("catch");
  env.catchExceptions(typeof catchingExceptions === "undefined" ? true : catchingExceptions);

  var throwingExpectationFailures = queryString.getParam("throwFailures");
  env.throwOnExpectationFailure(throwingExpectationFailures);

  var random = queryString.getParam("random");
  env.randomizeTests(random);

  var seed = queryString.getParam("seed");
  if (seed) {
    env.seed(seed);
  }

  /**
   * ## Reporters
   * The `HtmlReporter` builds all of the HTML UI for the runner page. This reporter paints the dots, stars, and x's for specs, as well as all spec names and all failures (if any).
   */
  var htmlReporter = new jasmine.HtmlReporter({
    env: env,
    onRaiseExceptionsClick: function() { queryString.navigateWithNewParam("catch", !env.catchingExceptions()); },
    onThrowExpectationsClick: function() { queryString.navigateWithNewParam("throwFailures", !env.throwingExpectationFailures()); },
    onRandomClick: function() { queryString.navigateWithNewParam("random", !env.randomTests()); },
    addToExistingQueryString: function(key, value) { return queryString.fullStringWithNewParam(key, value); },
    getContainer: function() { return document.body; },
    createElement: function() { return document.createElement.apply(document, arguments); },
    createTextNode: function() { return document.createTextNode.apply(document, arguments); },
    timer: new jasmine.Timer(),
    filterSpecs: filterSpecs
  });

  /**
   * The `jsApiReporter` also receives spec results, and is used by any environment that needs to extract the results  from JavaScript.
   */
  env.addReporter(jasmineInterface.jsApiReporter);
  env.addReporter(htmlReporter);

  /**
   * Filter which specs will be run by matching the start of the full name against the `spec` query param.
   */
  var specFilter = new jasmine.HtmlSpecFilter({
    filterString: function() { return queryString.getParam("spec"); }
  });

  env.specFilter = function(spec) {
    return specFilter.matches(spec.getFullName());
  };

  /**
   * Setting up timing functions to be able to be overridden. Certain browsers (Safari, IE 8, phantomjs) require this hack.
   */
  window.setTimeout = window.setTimeout;
  window.setInterval = window.setInterval;
  window.clearTimeout = window.clearTimeout;
  window.clearInterval = window.clearInterval;

  /**
   * ## Execution
   *
   * Replace the browser window's `onload`, ensure it's called, and then run all of the loaded specs. This includes initializing the `HtmlReporter` instance and then executing the loaded Jasmine environment. All of this will happen after all of the specs are loaded.
   */
  var currentWindowOnload = window.onload;

  window.onload = function() {
    if (currentWindowOnload) {
      currentWindowOnload();
    }
    htmlReporter.initialize();
    env.execute();
  };

  /**
   * Helper function for readability above.
   */
  function extend(destination, source) {
    for (var property in source) destination[property] = source[property];
    return destination;
  }

}());

```

在 Jasmine 的部分我們已經看到 `SpecRunner.html` 的內容，但在 **angular2** 本身的 test template 裡根本沒有這個檔案，到底是怎麼樣讓 test 跑起來?
其實 **angular2** 是使用 Karma 去做, test runner 的工作就是幫忙設置好 test framework 所需要的環境，以讓 test 的流程順利完成.

Karma 幫我們做的事情有下面幾項：

- [x] 1. 找到指定的檔案 (source, test spec) 與指定的 test framework (已安裝 jasmine)
- [x] 2. 將 1. 的檔案們組到一個暫時的 HTML 檔裡
- [x] 3. 同時開啟設定要跑 test 的瀏覽器們來跑 test
- [x] 4. 跑完關掉瀏覽器，把暫時的 HTML 檔案清除
- [x] 5. 透過設定 `autoWatch` 及 `singleRun`，可以在檔案有更新的時候,自動循環上面 1 ~ 3 步驟,即時看到 test 結果

---

##Jasmine :rocket:
[Jasmine](UnitTest/Jasmine.md)

##Karma :rocket:
[Karma](UnitTest/Karma.md)